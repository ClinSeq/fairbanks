# fairbanks

Development machine and staging server for the ALASCCA study at Karolinska Institutet.

Features:

* Centos7.1
* Slurm
* MSSQL ODBC driver
  
## Install prerequsistes and (py)autoseq in user space

(will require login for certain packages)

```bash
cd /nfs/ALASCCA
git clone https://github.com/ClinSeq/alascca-dotfiles.git
cd alascca-dotfiles
sh install-prereqs.sh
```

## "Start" production environment

```bash
. /nfs/ALASCCA/alascca-dotfiles/.bash_profile
```
