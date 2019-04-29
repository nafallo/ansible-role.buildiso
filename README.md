Ansible Role: buildiso
=========

This role makes remastering Ubuntu images easier. There is support for copying your own files to the installation media, and to sync a Personal Package Archive (PPA) from Launchpad.

Requirements
------------

The role requires the python jmespath libraries and also the `isohybrid`, `mkisofs` and `mksquashfs` binaries (the latter if you use the PPA sync functionality).
This role has only been tested by the author on Ubuntu 18.04.

Role Variables
--------------

Available variables are listed below, along with default values (see `defaults/main.yml`, `tasks/main.yml` and `vars/main.yml`):

Which Ubuntu image to fetch and customise, and where to place the resulting image. `buildiso_image` will be the result of `buildiso_output` with the name of the output image replaced by the input image.

    buildiso_input: http://cdimage.ubuntu.com/daily-live/current/disco-desktop-amd64.iso
    buildiso_output: custom.iso
    buildiso_image: disco-desktop-amd64.iso

Select which `preseed/<filename>.seed` to modify by setting `buildiso_preseed`. You could change this to a different one, but you still need to alter your `buildiso_menu` to make use of it.
You can point `buildiso_preseed_file` to a filename to include or you can set a variable `buildiso_preseed_content` to the content you would like to include in your preseed file.
The content of `preseed/ubuntu.seed` will always be included first in the file and you need to specify variables again to override them.
If both `buildiso_preseed_file` and `buildiso_preseed_content` has been defined, only `buildiso_preseed_file` will be used.

    buildiso_preseed: ubuntu
    buildiso_preseed_file: None
    buildiso_preseed_content: None

The boot menu of the image can be set to alternative options. There is no timeout in making the choice, and the default entry will be the default selection.
`buildiso_menu_hd` provides the option to boot from the first hard disk, and `buildiso_menu_memtest` lets the user start a memory test.

    buildiso_menu:
      - name: 'Try Ubuntu without installing'
        label: live
        preseed: ubuntu
      - name: 'Install Ubuntu'
        cmdline: only-ubiquity
        label: live-install
        preseed: ubuntu
      - name: 'OEM install (for manufacturers)'
        cmdline: 'only-ubiquity'
        pkgconf: 'oem-config/enable=true'
        preseed: ubuntu
      - name: 'Check disc for defects'
        cmdline: integrity-check
        label: check
    buildiso_menu_hd: true
    buildiso_menu_memtest: true

List of files to copy on to the image, if wanted. You only need to specify `src` if you are happy with the defaults.

    buildiso_files:
      src: random_file.txt
      dest: ^-- same as src
      mode: preserve

The `buildiso_name` variable sets the volume ID when generating the ISO file.

    buildiso_name: Ubuntu

When you require extra packages, this role provides support for syncing a PPA (Personal Package Archive) if the `buildiso_ppa` variable is set.
It uses the standard `ppa:team/ppa` structure. See the example playbook to see how you would synchronise the Ansible Stable PPA.

Example Playbook
----------------

    - hosts: localhost
      become: true
      roles:
        - { role: vcc-caeit.build_iso, buildiso_ppa: ppa:ansible/ansible }

License
-------

GPLv2

Author Information
------------------

This role was created in 2019 by Nafallo Bjälevik, whilst doing consultancy work for [Volvo Cars Corporation](http://www.volvocars.com/).