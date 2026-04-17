# MarkText Dev Toolchain Setup (Ubuntu 20.04)

## 1. Install Node.js 16

Node.js v16 is required (v17+ will not work).

```bash
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs
```

## 2. Install Yarn (without sudo)

Configure npm to use a user-local global directory to avoid permission errors:

```bash
mkdir -p ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
npm install -g yarn
```

Verify: `yarn --version`

## 3. Install system dependencies

```bash
sudo apt-get install -y \
  libx11-dev libxkbfile-dev \
  libsecret-1-dev libfontconfig-dev \
  python3 python3-distutils build-essential
```

## 4. Install project dependencies

```bash
cd /path/to/marktext
yarn install
```

## 5. Run in developer mode

```bash
yarn run dev
```

---

## Fork / Branch Workflow

Since you've already forked the repo, the standard contribution flow is:

1. **Add upstream remote** (once):
   ```bash
   git remote add upstream https://github.com/marktext/marktext.git
   ```

2. **Create a feature branch** off `develop`:
   ```bash
   git checkout -b my-feature develop
   ```

3. **Make changes, commit, and push** to your fork:
   ```bash
   git push origin my-feature
   ```

4. **Open a PR** from `your-fork/my-feature` → `marktext/develop`

### Keeping your fork in sync

```bash
git fetch upstream
git rebase upstream/develop   # run from your feature branch
```

If there are conflicts, resolve them then force-push:

```bash
git push -f origin my-feature
```

---

## Key contribution rules (from CONTRIBUTING.md)

- PRs must target the `develop` branch
- Run `yarn run lint` before submitting
- For **new features**: open an issue first, get approval, then submit the PR
- For **bug fixes**: title your PR as `fix: #<issue-number> short message`
- All PRs must pass CI checks before merging
