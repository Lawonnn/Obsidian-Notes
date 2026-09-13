> [!IMPORTANT]
> Whether we are performing client assessments, playing CTFs, taking a course in a Academy or elsewhere, or playing HTB boxes/labs, organization is always crucial

# Folder Structure
When attacking a single box, lab or client environment, we should have a clear folder structure on our attack machine to save data such as: scoping information, enumeration data, evidence of exploitation attempts, sensitive data such as credentials, and other data obtained during the recon, exploitation, and post-exploitation. A sample folder structure may look like:

![[Pasted image 20260818165641.png]]

Here we have a folder for the client ==Acme Company==, with 2 assessments:
- Internal Penetration Testing (IPT)
- External Penetration Testing (EPT)
Under each folder, we have subfolders for saving scan data, any relevant tools, logging output, scoping information (lists of IPs/networks to feed to our scanning tools)  and an evidence folder that may contain any credentials retrieved during the assessment, any relevant data retrieved as well as screenshots.