# Vagrant-Kali-Project-Setup-Tool
Use this tool to spin up a new Kali linux VM via vagrant, apply device encryption to the Kali VM, then create a companion bitlocker-protected synced virtual disk.

Imagine that you work on different engagements/projects in which Kali linux is one of your primary tools.  Maybe you also have the need to keep the data for each of those projects isolated from the others - in other words, you need to avoid cross-contamination of your projects.  This tool is for you if you need the following:

1. You use Windows as the host platform
2. For each project, you'd like a separate instance of Kali running in Virtualbox
3. And for each project, you'd like to have a separate bitlocker-protected virtual drive to store your data and other files in.  This virtual drive would also be setup as a Kali shared folder so you could seamless sync your Kali tool output to the virtual drive.

Here's what this tool does:

1. Creates a folder that will contain your new project-specific Vagrantfile and virtual disk (vhdx) file.
2. Creates your virtual drive, mounts it to a Windows drive letter (e.g., "G:\"), then bitlocker protects it.
3. Performs a "vagrant init" to build a project-specific Vagrantfile for your Kali box
4. Performs a "vagrant up" to create and provision your Kali linux VM (we assume Virtualbox is your VM provider)
5. Temporarily brings the box down with a "vagrant halt" so we can setup encryption on the Virtualbox storage device for your new VM
6. After setting up encryption, performs a "vagrant up" to bring the now-encrypted box back up.

At this point you can use familiar commands like "vagrant halt" and "vagrant up" to manage your Kali VM.  When you are finished with the project, you can "vagrant destroy" the box, and you'll still have your project data stored separately on your bitlocker-protected virtual disk.

Usage: copy down the Vagrantfile.erb template file and vagrant-kali-project-setup-tool.ps1 file to your Windows machine, then:

1. Open a cmd.exe console window as the administrator
2. powershell -executionpolicy bypass -file vagrant-kali-project-setup-tool.ps1

Notes:

1. We assume you are on a Windows host, of course, and we assume that you have vagrant and Virtualbox installed.
2. The script performs a few operations that require elevated privileges, namely creating/encrypting the virtual drive, and possibly patching vagrant.  That's why you need to run the powershell script in an elevated command window.
3. Vagrant (at least as of 2.2.5) needs patching to support the survival of the "vagrant up" operation at the point the boot process pauses to allow you to type in the virtualbox encryption password.  The tool attempts to see if the patch needs to be applied, then patches it if needed. Read the powershell script for a deeper discussion.  If you don't patch, then "vagrant up" aborts during the boot process, and certain things like folder sharing will fail.  This is all handled by the script.
4. In a multi-monitor configuration, certain pop-ups will appear on your "primary" monitor, which might not be the monitor you're looking at. When the tool asks you for a folder to create your project in, it will pop a Windows folder browser on the primary monitor.  You may have experienced this with other applications on Windows, where you sometimes have to look around to see where the popup is.

