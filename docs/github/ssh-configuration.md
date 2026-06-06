# How to configure SSH for GitHub

## 1. Generate a New SSH Key Pair

Open PowerShell or Git Bash and run the following command:
   
```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```
Use code with caution.(Replace your_email@example.com with your main GitHub email address).Press Enter to accept the default file saving location (~/.ssh/id_ed25519).Type a secure passphrase when prompted, or press Enter twice to leave it blank.s
## 2. Enable and Configure the Windows SSH Agent
To avoid entering your passphrase repeatedly, add your key to the background authentication manager.Open PowerShell as an Administrator.Run these commands to enable the native Windows OpenSSH Agent:powershell
```powershell
Get-Service ssh-agent | Set-Service -StartupType Automatic
Start-Service ssh-agent
```

Use code with caution.%%MAGIT_PARSER_PROTECT%%    3. Add your private key to the agent: %%MAGIT_PARSER_PROTECT%%  powershellssh-add ~.ssh\id_ed25519%%MAGIT_PARSER_PROTECT%%   ```


## 3. Add the Public Key to GitHub
You must register your public key file with your online profile.

1. Copy the public key text directly to your Windows clipboard by running:%%MAGIT_PARSER_PROTECT%%   powershell Get-Content ~\.ssh\id_ed25519.pub | Set-Clipboard %%MAGIT_PARSER_PROTECT%%   
2. Navigate to the GitHub SSH Settings Page.
3. Click New SSH key (or Add SSH key).
4. Provide a descriptive Title (e.g., "Windows Laptop").
5. Leave the key type as Authentication Key.
6. Paste your copied key into the Key field and click Add SSH key.


## Git Configuration

Run
```
  git config --global user.email "alexjinwonkim@gmail.com"
  git config --global user.name "Alex"

```