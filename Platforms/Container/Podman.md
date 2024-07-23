

`BindingCaps` in the context of `podman inspect` output refers to the **Bounding capability set** of a container. This set represents the capabilities that a container process can use. These capabilities are a subset of the full capabilities granted to the root user, allowing for more fine-grained control over what a container can do, enhancing security by limiting the container's privileges.

To set the bounding capabilities for a container in Podman, you can use the `--cap-add` and `--cap-drop` options when you create or run the container. Here's how you can use them:

- To **add** a capability:
  ```bash
  podman run --cap-add=CAP_NAME ...
  ```
- To **drop** a capability:
  ```bash
  podman run --cap-drop=CAP_NAME ...
  ```

Replace `CAP_NAME` with the actual capability you want to add or drop. For example, to add the `CAP_NET_BIND_SERVICE` capability, which allows the container to bind to well-known ports, you would use `--cap-add=CAP_NET_BIND_SERVICE`.

Other options related to capabilities include:
- `--privileged`: Gives the container full access to all devices and sets all capabilities.
- `--security-opt`: Allows you to override the default security settings.

For more detailed information on container capabilities and how to set them, you can refer to the official Podman documentation¹². Remember to always manage capabilities with caution, as they can affect the security of your container and host system.

Source: Conversation with Copilot, 14/6/2024
(1) podman-inspect — Podman documentation. https://docs.podman.io/en/latest/markdown/podman-inspect.1.html.
(2) podman-container-inspect — Podman documentation. https://docs.podman.io/en/latest/markdown/podman-container-inspect.1.html.
(3) podman-image-inspect — Podman documentation. https://docs.podman.io/en/latest/markdown/podman-image-inspect.1.html.