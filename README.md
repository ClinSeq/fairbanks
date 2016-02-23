# fairbanks

Development machine and staging server for the ALASCCA study at Karolinska Institutet.

Features:

* Centos6.7 (TODO update to Centos7)
* Slurm
  
### Scripts to run to install prerequsistes for (py)autoseq in user space:

```bash
sh install-prereqs.sh
```

This will assume that `$HOME/miniconda2/bin` is already in $PATH, so make sure to set it before. I do it with my set of [dotfiles](https://github.com/dakl/dotfiles).

