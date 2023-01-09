# Virtual Machine - Audio Passthrough
## VIRTUAL MACHINE - AUDIO PASSTHROUGH [Pulse Audio]

- Edit your virtual machine config
  - Replace `win10` by your vm name
  ```
  sudo EDITOR=nano virsh edit win10
  ```

  - Add at the bottom, in the `<qemu:commandline>` section:
    ```
    <qemu:arg value='-device'/>
    <qemu:arg value='ich9-intel-hda,bus=pcie.0,addr=0x1b'/>
    <qemu:arg value='-device'/>
    <qemu:arg value='hda-micro,audiodev=hda'/>
    <qemu:arg value='-audiodev'/>
    <qemu:arg value='pa,id=hda,server=unix:/run/user/1000/pulse/native'/>
    ```
- Edit `libvirt-qemu`
  ```
  sudo nano /etc/apparmor.d/abstractions/libvirt-qemu
  ```
  - Add this to the file
    ```
    /etc/pulse/client.conf.d/ r,
    /etc/pulse/client.conf.d/* r,
    /run/user/1000/pulse/native rw,
    ~/.config/pulse/* r,
    ~/.config/pulse/cookie k,
    ```
- Enable `PulseAudio` in systemd  
  Be aware that this can leak in security breach! [More on this](https://www.freedesktop.org/wiki/Software/PulseAudio/Documentation/User/SystemWide/)
  ```
  systemctl enable --user pulseaudio
  ```
- Reboot
  ```
  sudo reboot
  ```
