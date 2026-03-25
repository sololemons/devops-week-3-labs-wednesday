# SUID analysis

The Linux kernel ignores the SUID bit on interpreted scripts because of a race condition vulnerability in how scripts are executed. When a program is executed, the kernel must first open the file and then pass it to the interpreter specified in the shebang line (for example #!/bin/bash). In older systems, an attacker could modify or replace the script between the time the kernel checked the file permissions and the time the interpreter actually executed the file. This is called a time-of-check to time-of-use (TOCTOU) attack. Because this created a reliable way to execute arbitrary code as root, modern Linux kernels ignore the SUID bit on scripts entirely.

Even though the SUID bit itself has no effect on this script, the combination of SUID and world-write permissions still represents a critical finding because it indicates that untrusted users are able to modify a file that is executed in a privileged context. In this scenario, the script is executed by a root-owned cron job, meaning the script runs automatically with root privileges. World-write permissions allow any local user to change the contents of the script, so the privilege escalation happens through cron execution rather than the SUID mechanism. The presence of SUID also signals a misunderstanding of privilege controls, which often correlates with other misconfigurations.

This scenario becomes exploitable in practice when a privileged process such as cron, systemd, or a root user executes the script from a predictable location. An attacker with local access can modify the script to include malicious commands, such as adding a new sudo user, opening a reverse shell, or changing file permissions. When the scheduled task runs, the attacker’s code executes with root privileges. The exploit therefore depends on writable access to a file that is executed automatically by a privileged process, not on SUID functionality itself

## NB

In this scenario the deploy.sh had no SUID as in the script it had been removed arleady by 

```bash
chmod -R 777 /opt/kijanikiosk/
````
