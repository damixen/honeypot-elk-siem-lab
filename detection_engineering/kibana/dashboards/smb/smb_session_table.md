# SMB Session Table
## Purpose
These tables are used to observe SMB session behavior by flow ID/Source IP and to calculate the count of SMB commands. 
## Questions Answered
- How many READ/WRITE/DELETE/TRANS/IOCTL/DELETE commands did the session perform?
- Did the session succeed in session setup?
- When are the first and the last events recorded for the session?
- What's the duration of the session?
- How many commands did a country or an ASN have?

![[2026-06-16_09-37-02-smb-session-table-1.png]](../screenshots/2026-06-16_09-37-02-smb-session-table-1.png)

![[2026-06-16_09-37-20-smb-session-table-group-src-ip.png]](../screenshots/2026-06-16_09-37-20-smb-session-table-group-src-ip.png)

![[2026-06-16_09-37-52smb-session-table-country.png]](../screenshots/2026-06-16_09-37-52smb-session-table-country.png)

![[2026-06-16_09-38-26-smb-session-table-group-by-county-asn.png]](../screenshots/2026-06-16_09-38-26-smb-session-table-group-by-county-asn.png)