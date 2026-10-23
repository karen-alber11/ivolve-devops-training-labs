# Lab 5: Docker Image Vulnerability Scanning with Trivy

## Sequential Commands & Workflow

```bash
# 1. Clone the application and Dockerfile repository
git clone [https://github.com/Ibrahim-Adel15/TrivyImage.git](https://github.com/Ibrahim-Adel15/TrivyImage.git)
cd TrivyImage

# 2. Build the Docker image from the provided Dockerfile and tag it as trivy-lab:v1
docker build -t trivy-lab:v1 .

# 3. Download and add the official Trivy GPG key
wget -qO - [https://aquasecurity.github.io/trivy-repo/deb/public.key](https://aquasecurity.github.io/trivy-repo/deb/public.key) | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null

# 4. Add the Trivy repository to your system sources
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] [https://aquasecurity.github.io/trivy-repo/deb](https://aquasecurity.github.io/trivy-repo/deb) $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/trivy.list

# 5. Update package lists and install Trivy
sudo apt-get update && sudo apt-get install trivy -y

# 6. Verify the Trivy installation
trivy --version
-->nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab5$ trivy --version
Version: 0.71.2
Vulnerability DB:
  Version: 2
  UpdatedAt: 2026-10-22 19:14:47.059491382 +0000 UTC
  NextUpdate: 2026-10-23 19:14:47.059491102 +0000 UTC
  DownloadedAt: 2026-10-23 00:57:26.073792252 +0000 UTC
nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab5$ 

# 7. Scan the Docker image for all vulnerabilities
trivy image trivy-lab:v1

# 8. Scan the Docker image filtering for HIGH and CRITICAL vulnerabilities only
trivy image --severity HIGH,CRITICAL trivy-lab:v1
-->snap:
├─────────────────────────────┼─────────────────┼──────────┤              ├──────────────────────────────┼───────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ openssh-client              │ CVE-2026-60002  │ CRITICAL │              │ 1:8.4p1-5+deb11u5            │                               │ openssh: OpenSSH: Use-after-free vulnerability during host   │
│                             │                 │          │              │                              │                               │ key re-exchange on the client side...                        │
│                             │                 │          │              │                              │                               │ https://avd.aquasec.com/nvd/cve-2026-60002                   │
│                             ├─────────────────┼──────────┼──────────────┤                              ├───────────────────────────────┼──────────────────────────────────────────────────────────────┤
│                             │ CVE-2026-106553 │ HIGH     │ affected     │                              │                               │ openssh: OpenSSH: Credential disclosure via failed GSSAPI    │
│                             │                 │          │              │                              │                               │ authentication                                               │
│                             │                 │          │              │                              │                               │ https://avd.aquasec.com/nvd/cve-2026-106553                  │
│                             ├─────────────────┤          │              │                              ├───────────────────────────────┼──────────────────────────────────────────────────────────────┤


# 9. Generate a structured JSON vulnerability report
trivy image --severity HIGH,CRITICAL --format json -o report.json trivy-lab:v1

# 10. Vulnerability Remediation (Bonus):
#     - Update the base image inside the Dockerfile to a more secure/updated version (e.g., node:18-alpine)
#     - Rebuild the Docker image and tag it as ivolve-app:v2
docker build -t ivolve-app:v2 .

# 11. Rescan the updated image and generate a new remediation report
trivy image --severity HIGH,CRITICAL --format json -o report-v2.json ivolve-app:v2
-->nti@nti-virtual-machine:~/DevOps-Labs/Docker/Lab5$ trivy image --severity HIGH,CRITICAL --format json -o report-v2.json ivolve-app:v2
2026-10-23T04:05:09+03:00	INFO	[vuln] Vulnerability scanning is enabled
2026-10-23T04:05:09+03:00	INFO	[secret] Secret scanning is enabled
2026-10-23T04:05:09+03:00	INFO	[secret] If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2026-10-23T04:05:09+03:00	INFO	[secret] Please see https://trivy.dev/docs/v0.71/guide/scanner/secret#recommendation for faster secret detection
2026-10-23T04:05:10+03:00	INFO	Detected OS	family="debian" version="11.11"
2026-10-23T04:05:10+03:00	INFO	[debian] Detecting vulnerabilities...	os_version="11" pkg_num=409
2026-10-23T04:05:10+03:00	INFO	Number of language-specific files	num=1
2026-10-23T04:05:10+03:00	INFO	[node-pkg] Detecting vulnerabilities...
2026-10-23T04:05:10+03:00	WARN	Using severities from other vendors for some vulnerabilities. Read https://trivy.dev/docs/v0.71/guide/scanner/vulnerability#severity-selection for details.
2026-10-23T04:05:12+03:00	WARN	This OS version is no longer supported by the distribution	family="debian" version="11.11"
2026-10-23T04:05:12+03:00	WARN	The vulnerability detection may be insufficient because security updates are not provided

📣 Notices:
  - Version 0.76.1 of Trivy is now available, current version is 0.71.2

To suppress version checks, run Trivy scans with the --skip-version-check flag


