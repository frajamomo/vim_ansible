# Vim Ansible

Ansible playbooks to configure Vim with plugins, bash git prompt, and run system package updates across Fedora and Debian hosts.

## Playbooks

| Playbook | Description |
|----------|-------------|
| `vim-config.yml` | Install Vim with Pathogen plugin manager and deploy plugins and `.vimrc` |
| `bash-config.yml` | Deploy a bash git prompt (`PS1` with branch/status info) |
| `pkg_update.yml` | Update all system packages (dnf, apt, or Synology ipkg) and reboot if needed |
| `site.yml` | Run all of the above in sequence |
| `facts.yml` | Display system facts for a given host (utility/debug) |

## Vim Plugins

Managed via [Pathogen](https://github.com/tpope/vim-pathogen):

| Plugin | Description |
|--------|-------------|
| [vim-airline](https://github.com/vim-airline/vim-airline) | Status/tabline |
| [NERDTree](https://github.com/preservim/nerdtree) | File explorer (F5 or `,. `) |
| [fzf.vim](https://github.com/junegunn/fzf.vim) | Fuzzy file finder |
| [vim-gitgutter](https://github.com/airblade/vim-gitgutter) | Git diff signs in the gutter |
| [vim-fugitive](https://github.com/tpope/vim-fugitive) | Git integration |
| [vim-floaterm](https://github.com/voldikss/vim-floaterm) | Floating terminal (F12) |
| [ALE](https://github.com/dense-analysis/ale) | Async linting (Python flake8/pylint) |
| [ctrlp.vim](https://github.com/kien/ctrlp.vim) | Buffer/file switcher |

## Prerequisites

- Ansible 2.14+
- SSH key-based access to target hosts
- Sudo privileges on target hosts

## Setup

1. Clone the repository:
   ```bash
   git clone https://github.com/frajamomo/vim_ansible.git
   cd vim_ansible
   ```

2. Create and encrypt the vault file:
   ```bash
   cp inventory/group_vars/all/vault.yml.example inventory/group_vars/all/vault.yml
   vi inventory/group_vars/all/vault.yml    # fill in your values
   ansible-vault encrypt inventory/group_vars/all/vault.yml
   ```

## Usage

Deploy Vim configuration to all hosts:
```bash
ansible-playbook vim-config.yml --vault-password-file ~/.ansible-vault.txt
```

Deploy bash git prompt:
```bash
ansible-playbook bash-config.yml --vault-password-file ~/.ansible-vault.txt
```

Run everything (bash + vim + package updates):
```bash
ansible-playbook site.yml --vault-password-file ~/.ansible-vault.txt
```

Target a single host:
```bash
ansible-playbook vim-config.yml --vault-password-file ~/.ansible-vault.txt --limit raspberrypi
```

## Project Structure

```
vim_ansible/
├── ansible.cfg
├── site.yml                          # Master playbook
├── vim-config.yml                    # Vim setup playbook
├── bash-config.yml                   # Bash git prompt playbook
├── pkg_update.yml                    # Package update playbook
├── facts.yml                         # System facts utility
├── files/
│   └── gitps1.sh                     # Bash git prompt script
├── inventory/
│   ├── hosts.yml
│   └── group_vars/all/
│       ├── vault.yml                 # Encrypted secrets (gitignored)
│       └── vault.yml.example         # Template for vault.yml
└── vim/                              # Vim role
    ├── defaults/main.yml             # Plugin list
    ├── files/vimrc                   # .vimrc configuration
    ├── tasks/main.yml                # Install packages, plugins, config
    └── vars/main.yml                 # OS-aware package names
```
