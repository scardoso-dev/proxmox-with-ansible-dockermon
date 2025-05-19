# Automatisation de la création de VM Proxmox avec Ansible

## Objectif

Ce projet a pour but d'automatiser la création de machines virtuelles (VM) sur un hyperviseur Proxmox VE à l'aide d'Ansible, en supportant différents environnements (`dev`, `prd`) via une structure d'inventaire claire.

---

## Structure du projet

<code>
proxmox-with-ansible-dockermon/
├── collections/
│ └── requirements.yml # Dépendances Ansible (community.general)
├── inventories/
│ ├── dev/
│ │ ├── group_vars/all/ # Variables spécifiques à l'environnement dev
│ │ │ └── proxmox.yml
│ │ └── hosts # Fichier d'inventaire dev
│ └── prd/
│ ├── group_vars/all/
│ │ └── proxmox.yml
│ └── hosts # Fichier d'inventaire prd
├── playbooks/
│ └── create_vm.yml # Playbook principal
└── lubuntu-16.04.6-desktop-amd64.iso # Image ISO utilisée pour l'installation
</code>


---

## Prérequis

- Un hyperviseur **Proxmox VE** configuré et accessible en SSH
- Ansible installé sur le nœud de contrôle (Linux)
- Collection `community.general` installée :

```bash
ansible-galaxy collection install community.general
```

## Variables attendues (group_vars/all/proxmox.yml)
Exemple de fichier proxmox.yml :

<code>
proxmox_api_host: "192.168.30.140"
proxmox_user: "root@pam"
proxmox_password: "votre_mot_de_passe"
proxmox_node: "node0"
proxmox_vm_id: "200"
</code>

<br>

## Commandes d'exécution
Environnement de développement
```bash
ansible-playbook -i inventories/dev/hosts playbooks/create_vm.yml
```

Environnement de production
```bash
ansible-playbook -i inventories/prd/hosts playbooks/create_vm.yml
```

---

## Exemple de contenu du fichier d’inventaire (inventories/dev/hosts)
<code>
[dev]
127.0.0.1 ansible_connection=local
</code>
Remarque : ici l'exécution est en connection: local, car on cible l'API HTTP de Proxmox depuis le nœud de contrôle.


## Résultat attendu
- Une VM nommée vm-lubuntu est créée avec :
    - 2 vCPU, 512 Mo de RAM
    - Disque SCSI de 10 Go sur local-lvm
    - Image ISO montée en ide2
    - Interface réseau virtio bridgée sur vmbr0
- La VM est automatiquement démarrée à la fin du playbook.

---

## Auteurs
Samuel CARDOSO & Kevin TCHA
Projet réalisé dans le cadre du TP n°3 – automatisation de déploiement avec Ansible & Proxmox.