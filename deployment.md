# Deployment

## Deployment met Amazon Webservices

Voor AWS bestaan er publieke AMI's, voor europa is dat `ami-aa76d0dd`. Aangeraden is dat je deze deployed op een t2.medium instance.

Start deze server en connecteer ernaar via ssh (de user is 'ubuntu').

    chmod 400 {path-to-keypair}
    ssh -i {path-to-keypair} ubuntu@{public-ip}

Update dan de codebase

    sudo /edx/bin/update configuration release
    sudo /edx/bin/update edx-platform release

Indien U een 'Unable to resolve host' error krijgt, voeg dan nog het volgende toe aan `/etc/hosts`

    127.0.1.1 {whatever ip}
    
Indien er een fout is in de 'edx-platform release' voer dan het volgende uit

    cd /edx/app/edxapp/edx-platform
    sudo -u edxapp git remote prune origin
    
U kan nu verbinden met het LMS op poort 80 en het CMS op poort 18010. Indien er een 502 foutmelding word weergegeven, herstart dan de mongo ansible role

    cd /edx/app/edx_ansible/edx_ansible/playbooks && sudo /edx/app/edx_ansible/venvs/edx_ansible/bin/ansible-playbook -i localhost, -c local run_role.yml -e 'role=mongo' -e 'mongo_create_users=True'

De basisauthenticatie voor de site is:

    username: edx
    password: edx

De standaardlogingegevens zijn:

    user: staff@example.com
    password: edx
    
Indien U een 500 foutmelding krijgt, doe dan het volgende

    cd /edx/app/edxapp/edx-platform && sudo -u www-data /edx/bin/python.edxapp manage.py lms syncdb --migrate --settings aws
    cd /edx/app/edxapp/edx-platform && sudo -u www-data /edx/bin/python.edxapp manage.py cms syncdb --migrate --settings aws
    
Meer over het beheren van een fullstack:
https://github.com/edx/configuration/wiki/edX-Managing-the-Full-Stack
