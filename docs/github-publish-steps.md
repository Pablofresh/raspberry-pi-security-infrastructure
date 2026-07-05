# GitHub Publishing Steps

Run these commands from the parent folder that contains this repository.

```bash
cd raspberry-pi-security-infrastructure
git init
git add .
git commit -m "Initial Raspberry Pi security infrastructure portfolio project"
```

Then create a new empty repository on GitHub named:

```text
raspberry-pi-security-infrastructure
```

After creating the repository, GitHub will show the remote URL. Add it with:

```bash
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/raspberry-pi-security-infrastructure.git
git push -u origin main
```

Before pushing, review all files and confirm no secrets, full Tailscale details, passwords, private keys, or raw baseline archives are included.
```
