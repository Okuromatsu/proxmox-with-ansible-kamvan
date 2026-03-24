# 🚀 Proxmox VM Automation with Ansible

## 📌 Description

Ce projet permet d'automatiser la création et le démarrage de machines virtuelles (VM) sur un hyperviseur Proxmox en utilisant Ansible.

Il supporte plusieurs environnements :
- `dev` (développement)
- `prd` (production)

L'automatisation se fait via l'API Proxmox grâce au module `community.general.proxmox_kvm`.

---

## 🏗️ Architecture du projet

```
proxmox-with-ansible-kamvan/
│
├── inventories/
│   ├── dev/
│   │   ├── hosts
│   │   └── group_vars/
│   │       ├── all.yml
│   │       └── vault.yml
│   │
│   └── prd/
│       ├── hosts
│       └── group_vars/
│           ├── all.yml
│           └── vault.yml
│
├── playbooks/
│   └── create_vm.yml
│
├── ansible.cfg
├── requirements.yml
└── README.md
```

---

## ⚙️ Prérequis

### 🖥️ Machine de contrôle

- Linux recommandé
- Python 3
- Ansible installé

### 🧰 Installer Ansible

```
sudo apt update
sudo apt install ansible -y
```

### 📦 Installer les dépendances

```
ansible-galaxy collection install -r requirements.yml
apt install python3-proxmoxer
```

### 📤 Déploiement du projet

```
git clone https://github.com/Okuromatsu/proxmox-with-ansible-kamvan.git
cd proxmox-with-ansible-kamvan
```

---

## 🔑 Configuration

### 1. Configurer l’inventaire

Modifier le fichier :

```
inventories/dev/hosts
```

Exemple :

```
[proxmox]
proxmox-dev ansible_host=192.168.1.200
```

---

### 2. Configurer les variables globales

Fichier :

```
inventories/dev/group_vars/all.yml
```

Exemple :

```yaml
proxmox_node: "pve"
vm_name: "vm-dev"
vm_id: 200

memory: 512
cores: 2
sockets: 1

disk_size: "10G"
storage: "local-lvm"

iso: "local:iso/lubuntu.iso"
```

---

### 3. Configurer les credentials Proxmox

Fichier :

```
inventories/dev/group_vars/vault.yml
```

Exemple :

```yaml
proxmox_api_user: "root@pam"
proxmox_api_password: "your_password"
proxmox_api_host: "192.168.1.200"
```

---

## 🔐 Sécurisation avec Ansible Vault

### Chiffrer les secrets

```
ansible-vault encrypt inventories/dev/group_vars/vault.yml
```
### Modifier les secrets

```
ansible-vault edit inventories/dev/group_vars/vault.yml
```
### Exécuter avec Vault

```
ansible-playbook playbooks/create_vm.yml -e "env=dev" --ask-vault-pass
```
### Option automatisée (fichier password)

```
echo "monmotdepasse" > .vault_pass
chmod 600 .vault_pass
```

```
ansible-playbook playbooks/create_vm.yml -e "env=dev" --vault-password-file .vault_pass
```


> Pour utiliser l'environnement prd, il faut faire les configurations équivalentes dans les fichiers ```inventories/prd```

---

## 🚀 Utilisation

### ▶️ Créer et démarrer une VM (DEV)

```
ansible-playbook playbooks/create_vm.yml -e "env=dev"
```

---

### ▶️ Créer et démarrer une VM (PRD)

```
ansible-playbook playbooks/create_vm.yml -e "env=prd" -i inventories/prd/hosts
```

---

### 🧪 Mode simulation

```
ansible-playbook playbooks/create_vm.yml --check
```

---

## ⚙️ Fonctionnement

Le playbook :
1. S'assure de la présence des paquets nécessaire pour communiquer avec l'API de Proxmox.
2. Crée une VM sur Proxmox avec :
   - CPU, RAM, disque
   - ISO attaché
3. Démarre automatiquement la VM

---

## 🖥️ Vérification

Accéder à l’interface Proxmox :

```
https://IP_DU_PROXMOX:8006
```

Vérifier :
- VM créée
- VM démarrée

---

## 🔐 Sécurité

- Les identifiants Proxmox sont chiffrés avec Ansible Vault
- Aucun mot de passe en clair dans le dépôt
- `.vault_pass` doit être ignoré via `.gitignore`

---

## ✅ Résultat attendu

- Création automatique d’une VM
- Configuration complète
- Démarrage automatique
- Séparation des environnements dev / prd

---

## 👨‍💻 Auteur

Projet réalisé dans le cadre d’un TP d’automatisation avec Ansible et Proxmox.
