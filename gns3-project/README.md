# GNS3 project export

`Multi-Area-OSPF-Routing-GNS3-Lab.gns3project` is a cleaned archive of the saved GNS3 project from the local GNS3 workspace.

Included:

- The original GNS3 topology definition for 13 router nodes: R1–R12 and `Internet`.
- The 13 native Dynamips startup configurations under their original project paths.
- The original topology structure and interface links.

Excluded deliberately:

- Cisco IOS images and other proprietary vendor software.
- Dynamips runtime state such as NVRAM, bootflash, IOMEM, ghost files, locks, logs, and console output.

To use the archive, import it into GNS3 and map the referenced C7200 IOS image to a legally obtained local image. The archive has been structurally inspected, but live re-import and end-to-end OSPF verification remain unverified. The reference configs in `../configs/` are maintained separately for readability.
