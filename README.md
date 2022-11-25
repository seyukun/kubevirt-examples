# kubevirt-examples

You can use the following OS.

- [ubuntu:18.04](https://hub.docker.com/layers/docheio/containerdisk-ubuntu/18.04/images/sha256-33332ecf2a5853df4c9ca732958f339d4a28869516f2d0de8239fc578f969838?context=explore)
- [ubuntu:20.04](https://hub.docker.com/layers/docheio/containerdisk-ubuntu/20.04/images/sha256-85af275e351b746b1102785d1f4821b458a2fc2695edb8ac665f5725e3bb7f5d?context=explore)
- [ubuntu:22.04](https://hub.docker.com/layers/docheio/containerdisk-ubuntu/22.04/images/sha256-58c89e3a679948cb4daed81f7ef9b4ed0b29e4570903ecc8bf7dce64210596d5?context=explore)
- [centos:7-2009](https://hub.docker.com/layers/docheio/containerdisk-centos/7-2009/images/sha256-d74d97740ff894cf806aac255fef93bf9a2bbbb2cf7907cd4eba8a3c440a51bd?context=explore)
- [centos:8.4](https://hub.docker.com/layers/docheio/containerdisk-centos/8.4/images/sha256-2052443bf96dbaad5f40dbc44aca8777bd1021505e1239c54bf519a5f4a4488b?context=explore)
- [centos-stream:8](https://hub.docker.com/layers/docheio/containerdisk-centos-stream/8/images/sha256-f1b05d72ca7ed5baf77b4890f00764dbbb84259bd8ed77f40352ef723bd722cc?context=explore)
- [centos-stream:9](https://hub.docker.com/layers/docheio/containerdisk-centos-stream/9/images/sha256-c9089852a3f510c60b13105ffe3c6fc1fdf1f82fb1ade2f9889e6eeb4b4ba9a4?context=explore)
- [fedora:35](https://hub.docker.com/layers/docheio/containerdisk-fedora/35/images/sha256-aa7c41149156759ce91a12cf6967c200041f65ed9e5c5f5e4f1631407158ef63?context=explore)
- [fedora:36](https://hub.docker.com/layers/docheio/containerdisk-fedora/36/images/sha256-5b1f811292f645c9be8371a1666798e4c18618d6dc99e6ce086bd67c39908925?context=explore)
- [fedora:37](https://hub.docker.com/layers/docheio/containerdisk-fedora/37/images/sha256-e72db3a493ce58faa8a7269e1305603cf7c9984f06b8bd0598b146fbc0385dea?context=explore)
- [archlinux](https://hub.docker.com/layers/docheio/containerdisk-archlinux/0.1/images/sha256-c7c521134ccf397ff87ac6434beb3890e81fb203a29abd27ef4f32907a900cd7?context=explore)
- [opensuse-leap:15.3](https://hub.docker.com/layers/docheio/containerdisk-opensuse-leap/15.3/images/sha256-bdc783d3056f4de807b5f8227f9c911247fac1439f7d9ea0b451a4464a8a3d5f?context=explore)
- [opensuse-leap:15.4](https://hub.docker.com/layers/docheio/containerdisk-opensuse-leap/15.4/images/sha256-dd346b4173a5bfbeeba1438dc6af68732ddd3d0d09a05b037dd8a3858c08a395?context=explore)
- [gentoo:default](https://hub.docker.com/layers/docheio/containerdisk-gentoo/default/images/sha256-b503223751250a72ad5ca9e2364110edad8cec987c1f953105ef3764ac7b859c?context=repo) - not supported network
- [gentoo:systemd](https://hub.docker.com/layers/docheio/containerdisk-gentoo/systemd/images/sha256-452dd705c9c94ba87b024992b99502021a18cf2b84a71ddd84f4b4629eaf49c7?context=repo) - not supported network


```yaml
apiVersion: cdi.kubevirt.io/v1beta1
kind: DataVolume
metadata:
  name: example
spec:
  source:
    registry:
      url: "docker://docker.io/docheio/containerdisk-archlinux:0.1"
  pvc:
    accessModes:
      - ReadWriteOnce
    resources:
      requests:
        storage: 10Gi
---
apiVersion: kubevirt.io/v1
kind: VirtualMachine
metadata:
  labels:
    kubevirt.io/os: linux
  name: example
spec:
  running: true
  template:
    metadata:
      labels:
        kubevirt.io/domain: example
    spec:
      nodeSelector:
        kubevirt.io/schedulable: "true"
      domain:
        devices:
          disks:
          - disk:
              bus: virtio
            name: disk0
          - cdrom:
              bus: sata
              readonly: true
            name: cloudinitdisk
          interfaces:
          - name: default
            macAddress: '72:35:18:b7:59:7a'
            masquerade: {}
        machine:
          type: q35
        resources:
          requests:
            memory: 6G
            cpu: '6'
      networks:
      - name: default
        pod: {}
      volumes:
      - name: disk0
        dataVolume:
          name: example
      - name: cloudinitdisk
        cloudInitNoCloud:
         userData: |
            #cloud-config
            hostname: example
            ssh_pwauth: false
            disable_root: true
            ssh_authorized_keys:
            - ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIED25519ED25519ED25519ed25519+ED25519ed25519 user@local
            runcmd:
            - echo "I would recommend changing to a fast repository here."
---
apiVersion: v1
kind: Service
metadata:
  name: examle
spec:
  type: LoadBalancer
  selector:
    kubevirt.io/domain: examle
  ports:
  - protocol: TCP
    name: tcp22
    port: 22
    targetPort: 22
```
