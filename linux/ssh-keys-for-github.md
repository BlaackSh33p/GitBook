# SSH Keys for GitHub

## Setting up SSH Keys for GitHub on Windows Or Linux <a href="#a400" id="a400"></a>

Setting up an SSH key for GitHub on Windows is a simple process that involves generating an SSH key, adding it to the SSH agent, and linking it to your GitHub account. Here’s a step-by-step guide:

### 1. Check for Existing SSH Keys <a href="#a93e" id="a93e"></a>

Open a terminal (PowerShell, Git Bash, or Command Prompt) and run:

```bash
ls ~/.ssh
```

If there are files like id\_rsa and id\_rsa.pub, you already have an SSH key. If not, proceed to the next step.

### **2. Generate a New SSH Key** <a href="#f947" id="f947"></a>

Run the following command in your terminal:

```bash
ssh-keygen -t ed25519 -C "our_email@example.com"
```

Replace [your\_email@example.com](mailto:your_email@example.com) with your GitHub email address.

If your system doesn’t support ed25519, use RSA instead:\


```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

You’ll be prompted:

To save the file: Press Enter to save to the default location (\~/.ssh/id\_ed25519)

For a passphrase: Enter one for extra security or leave it empty.

### 3. Start the SSH Agent <a href="#c205" id="c205"></a>

On Windows In Git Bash  or On Linux:

```bash
eval “$(ssh-agent -s)”
ssh-add ~/.ssh/id_ed25519
```

In PowerShell if you are using standalone git binary from Mingw or any other source:

```bash
Start-Service ssh-agent
ssh-add C:\Users\YourUsername\.ssh\id_ed25519
```

Replace YourUsername with your actual Windows username.

If you’re using a Linux desktop environment, the SSH agent may already be running. You can skip the first command if you see output like Agent pid XXXX.

### **4. Add the SSH Key to Your GitHub Account** <a href="#id-566a" id="id-566a"></a>

View your public key:

```bash
# on windows
Get-Content C:\Users\YourUsername\.ssh\id_ed25519.pub
# on linux
cat /home/YourUsername/.ssh/id_ed25519.pub
```

Log in to GitHub

Go to Settings > SSH and GPG keys.

Click New SSH Key.

Paste the public key into the “Key” field.

Add a title and click Add SSH Key.

### **5. Test the Connection** <a href="#ede5" id="ede5"></a>

Run the following command to verify your SSH setup:

```bash
ssh -T git@github.com
```

If successful, you’ll see:

Hi username! You’ve successfully authenticated, but GitHub does not provide shell access.

### &#x20;<a href="#id-01bb" id="id-01bb"></a>
