- [macvtap device](#macvtap-device)
  - [performance implications of using MacVTap](#performance-implications-of-using-macvtap)
  - [Configuring a **MacVTap** device in KVM](#configuring-a-macvtap-device-in-kvm)
  - [Questions](#questions)
  - [References](#references)

# macvtap device

A **macvtap device** is a newer Linux kernel driver that simplifies virtualized bridged networking. It replaces the combination of the `tun/tap` and bridge drivers with a single module based on the `macvlan` device driver. Here's how it works:

- **Purpose**: MacVTap is most useful for virtualization scenarios.
- **Functionality**: It allows both the guest and the host to appear directly on the network switch to which the host is connected.
- **Advantages**:
  - Improves throughput and reduces latencies to external systems.
  - Shortens the code path by bypassing components associated with software bridges.
- **Modes**:
  - **VEPA (Virtual Ethernet Port Aggregator)**: Data flows from one endpoint down through the source device in the KVM host to the external switch. If the switch supports hairpin mode, data is sent back to the source device and then to the destination endpoint.
  - **Bridge Mode**: Connects all endpoints directly to each other, allowing direct frame exchange without an external bridge.
  - **Private Mode**: Similar to VEPA mode but restricted to communication within the same lower device²³.
Remember that while MacVTap simplifies networking, the host won't be able to communicate with guests via this network directly.

## performance implications of using MacVTap

From a performance perspective, the **MacVTap** driver consistently demonstrates higher throughput and better CPU efficiency compared to software bridges. Here are the key points:

1. **Throughput**:
   - MacVTap provides exceptional transactional throughput, often 10-50% better than software bridges.
   - As load increases, MacVTap scales up throughput more quickly than using a software bridge.

2. **Codepath Optimization**:
   - MacVTap shortens the codepath by bypassing components associated with software bridges.
   - This reduction in code execution usually improves throughput and reduces latencies to external systems.

However, MacVTap has limitations:

- It doesn't readily enable network communication between the KVM host and guests.
- It must attach to a physical host interface, exposing guests to external network traffic.

In summary, MacVTap excels in performance but requires careful consideration based on your specific use case.

## Configuring a **MacVTap** device in KVM

1. **Check Kernel Support**:
   - Ensure your Linux kernel supports the **macvlan** driver. Run the following commands to verify:

     ```
     modprobe macvlan
     lsmod | grep macvlan
     ```

   - If you encounter errors or no results, your kernel might not support it. Most recent distributions should work fine¹.

2. **Define a Libvirt Network**:
   - Create a Libvirt network that uses **macvtap** interfaces associated with a physical interface (e.g., `eth1`):

     ```xml
     <network>
         <name>macvtap-net</name>
         <forward mode="bridge">
             <interface dev="eth1"/>
         </forward>
     </network>
     ```

   - Use `virsh net-define` with the XML definition to create the network:

     ```
     virsh net-define macvtap-def.xml
     ```

   - Set the network to autostart and start it:

     ```
     virsh net-autostart macvtap-net
     virsh net-start macvtap-net
     ```

3. **Create a KVM Guest Domain**:
   - Use `virt-install` (or your preferred tool) to create a KVM guest domain attached to the new Libvirt network.
   - For example, using CirrOS:

     ```bash
     virt-install --name=cirros --ram=256 --vcpus=1 \
         --disk path=/home/user/cirros-0.3.2-x86_64-disk.img,format=qcow2 \
         --import --network network:macvtap-net,model=virtio --vnc
     ```

   - Adjust the path to the CirrOS image as needed.

4. **Verify the MacVTap Interface**:
   - After the guest starts, check the new **macvtap** interface:

     ```
     ip link list
     ```

   - You'll see an entry similar to:

     ```
     5: macvtap0@eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ...
     ```

Remember that the MAC addresses on the host and guest will match since it's essentially the same interface in both places.
[IBM tutorial](https://www.ibm.com/docs/en/linux-on-systems?topic=configurations-kvm-guest-virtual-network-configuration-using-macvtap) provides additional details if needed.

Source: Conversation with Copilot, 2/8/2024

## Questions

- How do I configure a MacVTap device in KVM?
- What are the performance implications of using MacVTap?
- Are there any limitations or considerations when using macvtap devices?
- How can I measure the performance impact of MacVTap in my environment?
- What scenarios benefit most from using MacVTap?
- Are there any real-world benchmarks comparing MacVTap to other solutions?


## References

- Macvtap - ArchWiki. <https://wiki.archlinux.org/title/Macvtap>.
- MacVTap - Wikipedia. <https://en.wikipedia.org/wiki/MacVTap>.
- Using the MacVTap driver - IBM. <https://www.ibm.com/docs/en/linux-on-systems?topic=choices-using-macvtap-driver>.
- MacVTap - Linux Virtualization Wiki - Kernel_Newbies. <https://virt.kernelnewbies.org/MacVTap>.
- MacVTap driver considerations - IBM. <https://www.ibm.com/docs/sl/linux-on-systems?topic=cons-macvtap-driver-considerations>.
- Using the MacVTap driver - IBM. <https://www.ibm.com/docs/en/linux-on-systems?topic=choices-using-macvtap-driver>.
- KVM Network Performance - Best Practices and Tuning Recommendations. <https://docslib.org/doc/8867041/kvm-network-performance-best-practices-and-tuning-recommendations>.
- Using KVM with Libvirt and macvtap Interfaces - Scott's Weblog. <https://blog.scottlowe.org/2016/02/09/using-kvm-libvirt-macvtap-interfaces/>.
- Howto do QEMU full virtualization with MacVTap networking. <https://ahelpme.com/linux/howto-do-qemu-full-virtualization-with-macvtap-networking/>.
- KVM guest virtual network configuration using MacVTap - IBM. <https://www.ibm.com/docs/en/linux-on-systems?topic=configurations-kvm-guest-virtual-network-configuration-using-macvtap>.
- Using the MacVTap driver - IBM. <https://www.ibm.com/docs/en/linux-on-systems?topic=choices-using-macvtap-driver>.
- github.com. <https://github.com/scottslowe/lowescott.github.io/tree/243124172eeabec5d7631bcc6afe3b0a9abb29a4/_posts%2F2016-02-09-using-kvm-libvirt-macvtap-interfaces.md>.
