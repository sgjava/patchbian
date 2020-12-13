![Title](images/title.png)

So you want to submit a patch for Armbian, but you are not sure how to do this.
Well, I just did my first patch and I'm going to tell you how. What I've done
is improved the workflow with GitHub CLI, so you can do everything from a
command prompt without having to use the web UI (except deleting forked repo).
* Create an Ubuntu 20.04 VM with VirtualBox or other hypervisor.
* Install Github CLI
    * `sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-key C99B11DEB97541F0`
    * `sudo apt-add-repository https://cli.github.com/packages`
    * `sudo apt update`
    * `sudo apt -y install gh git`
* Configure git
    * `git config --global user.email "your@email.com"`
    * `git config --global user.name "Your Name"`
* Generate gpg key
    * `gpg --generate-key`
* Generate Github login [token](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/creating-a-personal-access-token)
* Login to Github (you only have to do the steps above once)
    * `gh auth login --with-token <<< 'your_token'`
* Fork armbian/build, clone and add remote
* `gh repo fork armbian/build --clone=true --remote=true`
* Create branch
    * `cd build`
    * `git checkout -b your_branch_name`
        * Change branch name for your patch
* Compile Armbian (this use case is to change something in the device tree and test the built image)
    * `./compile.sh CREATE_PATCHES="yes"`
        * Full OS image for flashing
        * Do not change kernel configuration
        * Chose a board
        * Chose a kernel
        * Chose a release package base
        * Chose image type
        * Chose image type
        * Configuring apt-cacher-ng
* Wait for prompt to make u-boot changes (press Enter after making changes in specified directory)
    * `[ o.k. ] * [l][c] enable-distro-bootcmd.patch` 
    * `[ warn ] Make your changes in this directory: [ /home/yourhome/build/cache/sources/u-boot-odroidxu/odroidxu4-v2017.05 ]`
    * `[ warn ] Press <Enter> after you are done [ waiting ]`
* Open another terminal or Files window
* In this case I want to add gpio-line-names to the Odroid UX4 device tree when prompted to do so
    * `sudo nano build/cache/sources/linux-odroidxu4/odroid-5.4.y/arch/arm/boot/dts/exynos5420-pinctrl.dtsi`
* Wait for prompt to make kernel changes (press Enter after making changes in specified directory)
    * `[ warn ] Make your changes in this directory: [ /home/yourhome/build/cache/sources/linux-odroidxu4/odroid-5.4.y ]`
    * `[ warn ] Press <Enter> after you are done [ waiting ]`
* Test changes you made on your board
    * Mine was located in `/home/yourhome/build/output/images/Armbian_21.02.0-trunk_Odroidxu4_focal_current_5.4.83.img`
* Rename patch to something meaningful and move to proper location
    * `mv output/patch/kernel-odroidxu4-current.patch patch/kernel/odroidxu4-current/add-gpio-line-names.patch`
* Add patch
    * `git add patch/kernel/odroidxu4-current/add-gpio-line-names.patch`
* Commit patch
    * `git commit -S -m "XU4 add gpio-line-names"`
* Create pull request (select armbian/build as base repo and push branch to your fork)
    * `gh pr create --title "XU4 add gpio-line-names" --body "Aded gpio-line-names, connector and physical pin number"`
    `? Which should be the base repository (used for e.g. querying issues) for this directory? armbian/build`
    `? Where should we push the 'add-gpio-line-names' branch? yourgithub/build`
    ``
    `Creating pull request for yourgithub:add-gpio-line-names into master in armbian/build`
    ``
    `Username for 'https://github.com': your@email.com`
    `Password for 'https://your@email.com@github.com': `
    `remote:` 
    `remote:` 
    `To https://github.com/yourgithub/build.git`
    ` * [new branch]        HEAD -> add-gpio-line-names`
    `Branch 'add-gpio-line-names' set up to track remote branch 'add-gpio-line-names' from 'origin'.`
    `https://github.com/armbian/build/pull/prnum`
    