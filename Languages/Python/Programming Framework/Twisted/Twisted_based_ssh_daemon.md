```python
from twisted.conch import avatar, recvline, interfaces as conchinterfaces
from twisted.conch.ssh import factory, keys, session, userauth, connection, transport
from twisted.cred import portal, checkers
from twisted.internet import reactor, defer
from zope.interface import implementer
import os

# Path to the server keys and authorized_keys
HOST_KEY_PATH = os.path.expanduser("server_rsa.key")
AUTHORIZED_KEYS_PATH = os.path.expanduser("authorized_keys")

# Generate host key if not exists
if not os.path.exists(HOST_KEY_PATH):
    from twisted.conch.ssh.keys import Key
    key = Key.generate(2048)
    with open(HOST_KEY_PATH, 'wb') as f:
        f.write(key.toString('PEM'))

@implementer(conchinterfaces.ISession)
class EchoSession:
    def __init__(self, avatar):
        self.avatar = avatar

    def execCommand(self, proto, cmd):
        proto.write(f"Running: {cmd.decode()}\n")
        proto.write(os.popen(cmd.decode()).read())
        proto.loseConnection()

    def openShell(self, transport):
        serverProtocol = recvline.HistoricRecvLine()
        serverProtocol.makeConnection(transport)
        transport.makeConnection(session.wrapProtocol(serverProtocol))

    def eofReceived(self): pass
    def closed(self): pass

@implementer(conchinterfaces.IConchUser)
class SSHUser(avatar.ConchUser):
    def __init__(self, username):
        super().__init__()
        self.username = username
        self.channelLookup.update({b'session': session.SSHSession})
        self.subsystemLookup = {}

    def getSession(self, _):
        return EchoSession(self)

@implementer(portal.IRealm)
class SSHRealm:
    def requestAvatar(self, avatarId, mind, *interfaces):
        return interfaces[0], SSHUser(avatarId), lambda: None

class PublicKeyChecker(checkers.SSHPublicKeyChecker):
    def __init__(self):
        super().__init__(self)
        self.valid_keys = self._load_keys()

    def _load_keys(self):
        if not os.path.exists(AUTHORIZED_KEYS_PATH):
            return []
        with open(AUTHORIZED_KEYS_PATH, 'r') as f:
            return [keys.Key.fromString(data=line.encode()) for line in f if line.strip()]

    def checkKey(self, credentials):
        for key in self.valid_keys:
            if credentials.blob == key.blob():
                return defer.succeed(credentials.username)
        return defer.fail(UnauthorizedLogin("No matching public key"))

def getPrivateKey():
    with open(HOST_KEY_PATH, 'rb') as f:
        return keys.Key.fromString(f.read())

def run_ssh_server(port=2222):
    sshFactory = factory.SSHFactory()
    sshFactory.portal = portal.Portal(SSHRealm())
    sshFactory.portal.registerChecker(PublicKeyChecker())
    sshFactory.publicKeys = {
        b'ssh-rsa': getPrivateKey().public()
    }
    sshFactory.privateKeys = {
        b'ssh-rsa': getPrivateKey()
    }

    reactor.listenTCP(port, sshFactory)
    print(f"SSH server running on port {port}")
    reactor.run()

if __name__ == "__main__":
    run_ssh_server()

```

How to Use

1. Save your SSH public key in a file called `authorized_keys` (same format as ``.ssh/authorized_keys`).


2. Run this script as a non-root user. It listens on port 2222 by default (you can change it).


3. Connect using:

`ssh -p 2222 user@localhost -i your_private_key`

Note: "user" can be any name; it just matches what you signed in with using your SSH key.
