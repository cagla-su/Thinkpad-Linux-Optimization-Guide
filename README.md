# Thinkpad Linux Optimization Guide (Incomplete)
Hello. In this guide, you will be informed about how to optimize your Thinkpad device while using Linux. If you are ready, let's begin!
# Before Starting
- This guide will be **mainly about Thinkpad T490**. My Thinkpad T490 has the specifications below:
  - **Processor** - `Intel® Core™ i5-8265U (8) @ 1.6 GHz - 3.90 GHz`
  - **Graphics Card** - `Intel UHD Graphics 620 @ 1.1 GHz`
  - **RAM** - `16 GB dual-channel`
- There will be **undervolting** and other **hardware related software optimizations** in the guide, which means you **should not copy the same values** on steps that you are warned! However, most of the steps should work the same on all Thinkpad (even non Thinkpad) devices.
# Get Started
## Distribution Suggestions
- For **the maximum stability**
  - <img width="16" height="25" alt="image" src="https://github.com/user-attachments/assets/065bc13d-97f3-4c53-9c74-e15046dc6552" /> **MX Linux**
  - <img width="16" height="25" alt="image" src="https://github.com/user-attachments/assets/39f781e5-9fd1-4828-b0ab-bbfb35630636" /> **Linux Mint**
  - <img width="16" height="25" alt="image" src="https://github.com/user-attachments/assets/d58caa55-0b42-4643-9a0a-5751ea57c8cc" /> **Zorin**
- For having **the most up to date packages**
  - <img width="16" height="25" alt="image" src="https://github.com/user-attachments/assets/8f0cf4e0-7653-4ad9-ade3-5e70fa83e444" /> **Arch Linux**
  - <img width="16" height="25" alt="image" src="https://github.com/user-attachments/assets/15ccfca8-e397-4b65-a486-ebdd929407a2" /> **EndeavourOS**
  - <img width="16" height="25" alt="image" src="https://github.com/user-attachments/assets/ec6b02a8-c62b-447b-b03b-3d546c4e9e1a" /> **CachyOS** (good for gaming performance)
- For **the balance of stability and up to date packages**
  - <img width="16" height="25" alt="image" src="https://github.com/user-attachments/assets/8f714534-3c7a-44b2-8ca5-9685a551dd94" /> **Fedora**
# Increase Audio Quality
Laptop speakers sound worse on Linux. In order to enhance your speaker experience:
- Install <img width="16" height="25" alt="image" src="https://github.com/user-attachments/assets/9dab98c4-08f9-4cf4-9f5c-3eff8746ab55" /> **EasyEffects** using your package manager or Flatpak.
- Next, you can prefer either [one of the community presets](https://github.com/wwmm/easyeffects/wiki/Community-presets) or [my own preset](https://github.com/cagla-su/Thinkpad-Linux-Optimization-Guide/releases)
  - In my own preset, the steps you should apply are explained.
# Increase Battery Life and Performance
## Kernel Parameter
### For systemd-boot
```
ls /boot/loader/entries/
```
Now find your kernel name. The name can differ according to the kernel and distribution you use. It can be `arch.conf`, `linux.conf`, `linux-cachyos.conf` etc.
- After finding your kernel's configuration file:
```
sudo nano /boot/loader/entries/yourkernel.conf
```
Find the line that starts with `Options` and add these two to the end of the line:
```
mitigations=off rcutree.enable_rcu_lazy=1
```
Lastly, save the file and reboot your system.
### For GRUB
```
sudo nano /etc/default/grub
```
Now find the line that starts with `GRUB_CMDLINE_LINUX_DEFAULT` and add these two to the end of the line:
```
mitigations=off rcutree.enable_rcu_lazy=1
```
Next, save the file and update GRUB configuration file according to your distribution:
- <img width="16" height="25" alt="image" src="https://github.com/user-attachments/assets/c56a6d0a-133d-4bcf-a594-f4cd8b58e335" /> <img width="16" height="25" alt="image" src="https://github.com/user-attachments/assets/7960ea90-ac89-4e3a-9839-cd77f9ec2b24" /> **For Debian/Ubuntu and Derivatives:**
```
sudo update-grub
```
- <img width="16" height="25" alt="image" src="https://github.com/user-attachments/assets/8f0cf4e0-7653-4ad9-ade3-5e70fa83e444" /> **For Arch Linux and Derivatives:**
```
sudo grub-mkconfig -o /boot/grub/grub.cfg
```
- <img width="16" height="25" alt="image" src="https://github.com/user-attachments/assets/8f714534-3c7a-44b2-8ca5-9685a551dd94" /> **For Fedora and Derivatives:**
```
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```
> [!NOTE]
- The `rcutree.enable_rcu_lazy=1` kernel parameter reduces power usage when your system is **idle** or **lightly loaded**. In exchange for power saving, it might **reduce your performance** but you probably will not feel the difference on performance at all.
- The `mitigations=off` kernel parameter simply disables the patches applied for hardware-based security vulnerabilities, which increases performance on these processors:
  - <img width="16" height="25" alt="intel" src="https://github.com/user-attachments/assets/315abff4-87d6-4779-b38d-08f07b8237a4" /> **Intel -** *8th gen and older processors*
  - <img width="16" height="32" alt="image" src="https://github.com/user-attachments/assets/bea33d54-3560-4d14-a706-6a4edef4e8a6" /> **AMD -** *Zen 1, Zen+ and Zen 2 processors*
- So if you have **a newer processor**, you **do not** have to use this kernel parameter. Plus, it **creates a huge security risk**. **Do not use** this kernel parameter if you **do not want to maximize performance** on your old hardware.
## Power Manager
- I definitely suggest that you use **TLP** because it is **more advanced** than other power managers.
- However, your system might already have another power manager installed, distributions nowadays use either `power-profiles-daemon` or `tuned`. Try removing each to make sure they are not installed.
- Install `tlp` using your package manager.
```
sudo nano /etc/tlp.conf # make sure to uncheck each option mentioned below
```
```
TLP_ENABLE=1

DISK_IDLE_SECS_ON_AC=0
DISK_IDLE_SECS_ON_BAT=2

MAX_LOST_WORK_SECS_ON_AC=15
MAX_LOST_WORK_SECS_ON_BAT=60

CPU_DRIVER_OPMODE_ON_AC=active
CPU_DRIVER_OPMODE_ON_BAT=active

CPU_SCALING_GOVERNOR_ON_AC=performance
CPU_SCALING_GOVERNOR_ON_BAT=powersave

CPU_ENERGY_PERF_POLICY_ON_AC=performance
CPU_ENERGY_PERF_POLICY_ON_BAT=power

CPU_MIN_PERF_ON_AC=0
CPU_MAX_PERF_ON_AC=100
CPU_MIN_PERF_ON_BAT=0
CPU_MAX_PERF_ON_BAT=20

CPU_BOOST_ON_AC=1
CPU_BOOST_ON_BAT=0

CPU_HWP_DYN_BOOST_ON_AC=1
CPU_HWP_DYN_BOOST_ON_BAT=0

NMI_WATCHDOG=0

PLATFORM_PROFILE_ON_AC=performance
PLATFORM_PROFILE_ON_BAT=low-power

AHCI_RUNTIME_PM_ON_AC=on
AHCI_RUNTIME_PM_ON_BAT=auto

INTEL_GPU_MIN_FREQ_ON_AC=1100 # check the frequencies your Intel GPU supports before entering these values
INTEL_GPU_MIN_FREQ_ON_BAT=300
INTEL_GPU_MAX_FREQ_ON_AC=1100
INTEL_GPU_MAX_FREQ_ON_BAT=400
INTEL_GPU_BOOST_FREQ_ON_AC=1100
INTEL_GPU_BOOST_FREQ_ON_BAT=400

WIFI_PWR_ON_AC=off
WIFI_PWR_ON_BAT=on

WOL_DISABLE=Y

SOUND_POWER_SAVE_ON_AC=0
SOUND_POWER_SAVE_ON_BAT=1

PCIE_ASPM_ON_AC=performance
PCIE_ASPM_ON_BAT=powersupersave

RUNTIME_PM_ON_AC=on
RUNTIME_PM_ON_BAT=auto

START_CHARGE_THRESH_BAT0=85
STOP_CHARGE_THRESH_BAT0=90
```
## Undervolting (Intel Only)
- Undervolting makes your computer **consume less power without reducing performance**.
  - In fact, it **might increase your performance** if your computer intentionally reduces your performance due to overheating issue.
  - Since your computer consumes less power, it will **naturally increase battery life**. So, it is a win-win.
- However, undervolting **will not work** on <img width="16" height="25" alt="intel" src="https://github.com/user-attachments/assets/315abff4-87d6-4779-b38d-08f07b8237a4" /> Intel **10th gen and later** processors.
- Install [throttled](https://github.com/erpalma/throttled) making sure your system **does not** have `thermald` package installed.
```
sudo nano /etc/throttled.conf
```
- These values are the stable values for **Thinkpad T490**. So please **do not copy the same values** as they might **crash your system**!!!
- You can get help from AI for finding your own stable values. However, I can at least say that going **beyond -60** for Intel GPUs are generally **unstable**.
```
[BATTERY]
Update_Rate_s: 60
PL1_Tdp_W: 8
PL1_Duration_s: 28
PL2_Tdp_W: 12
PL2_Duration_S: 5
Trip_Temp_C: 60
cTDP: 1
Disable_BDPROCHOT: False

[AC]
Update_Rate_s: 5
PL1_Tdp_W: 21
PL1_Duration_s: 28
PL2_Tdp_W: 25
PL2_Duration_S: 0.002
Trip_Temp_C: 95
HWP_Mode: False
cTDP: 2
Disable_BDPROCHOT: False

[UNDERVOLT.BATTERY]
CORE: -105
GPU: -60
CACHE: -105
UNCORE: -60
ANALOGIO: 0

[UNDERVOLT.AC]
CORE: -90
GPU: -60
CACHE: -90
UNCORE: -60
ANALOGIO: 0
```
## Disabling NetworkManager-wait-online.service
Disabling `NetworkManager-wait-online.service` allows your system to load faster.
```
sudo systemctl disable NetworkManager-wait-online.service
```
# Customization
## Login Screen Customization
I suggest [sddm-astronaut-theme](https://github.com/Keyitdev/sddm-astronaut-theme) for a better login screen.
- There are multiple themes included in the project, there are also animated themes if you prefer.
- Even if you use a desktop environment that does not use SDDM, the script will replace it with SDDM and it will work after a reboot.
## <img width="16" height="25" alt="image" src="https://github.com/user-attachments/assets/a4a4ce43-0e32-406f-951a-8761be2f9c5e" /> Terminal Customization
**Fish** is a sweet command line shell that predicts what you are going to type based on your previous terminal commands. **Fastfetch** is a system information tool that makes your terminal look better.
- Install `fish` and `fastfetch` according to your package manager.
```
chsh -s /usr/bin/fish # you should reboot after running the command
```
- If terminal tells you that the **process has failed**, try `chsh -s /bin/fish` instead.
- Additionally, if you would like to see **fastfetch** every time you launch terminal, you should execute the commands below:
```
  function fish_greeting
  fastfetch
  end
```
```
funcsave fish_greeting
```
Lastly, if you would like to try, this is my custom fastfetch appearance:
<img width="712" height="375" alt="image" src="https://github.com/user-attachments/assets/4839909f-dc9a-43f0-afca-14f3ac4a2dd8" />
```
sudo mkdir ~/.config/fastfetch/ && sudo nano ~/.config/fastfetch/config.jsonc
```
```
{
  "$schema": "https://github.com/fastfetch-cli/fastfetch/raw/dev/doc/json_schema.json",
"logo": {
"type": "small",
"padding": {
        "top": 6,
        "left": 3
    },
"color": {
"1": "magenta",
"2": "magenta"
}
},
"modules": [
    // Title
    {
      "type": "title",
      "format": "{#1}╭───────────────────"
    },
    // System Information
    {
      "type": "custom",
      "format": "{#1}│ {#}>^^< System Information >^^<"
    },
{
      "type": "host",
      "key": "│ Computer Model",
      "keyColor": "magenta"
    },
    {
      "type": "os",
      "key": "│ Operating System",
      "keyColor": "magenta"
    },
    {
      "type": "kernel",
      "key": "│ Kernel",
      "keyColor": "magenta"
    },
{
      "type": "packages",
      "key": "│ Packages",
      "keyColor": "magenta"
    },
    {
      "type": "custom",
      "format": "{#1}│"
    },
    // Desktop
    {
      "type": "custom",
      "format": "{#1}│ {#}>^^< Desktop >^^<",
    },
    {
      "type": "de",
      "key": "│ Desktop Environment",
      "keyColor": "magenta"
    },
    {
      "type": "wm",
      "key": "│ Window Manager",
      "keyColor": "magenta"
    },
    {
      "type": "shell",
      "key": "│ Shell",
      "keyColor": "magenta"
    },
    {
      "type": "custom",
      "format": "{#1}│"
    },
    // Hardware Information
    {
      "type": "custom",
      "format": "{#1}│ {#}>^^< Hardware Information >^^<",
    },
    {
      "type": "cpu",
      "key": "│ Processor",
      "keyColor": "magenta"
    },
    {
      "type": "gpu",
      "key": "│ Graphics Card",
      "keyColor": "magenta"
    },
    {
      "type": "memory",
      "key": "│ Memory",
      "keyColor": "magenta"
    },
    {
      "type": "disk",
      "key": "│ Disk",
      "keyColor": "magenta"
    },
    {
      "type": "custom",
      "format": "{#1}│"
    },
    // Colors
    {
      "type": "colors",
      "key": "{#separator}│",
      "symbol": "circle"
    },
    // Footer
    {
      "type": "custom",
      "format": "{#1}╰───────────────────"
    }
  ]
}
```
