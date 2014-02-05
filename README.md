GDI - Intro to Servers - Tools
==============================

Requirements: 
  
  * __ansible__ - only supported on Mac and Unix systems
    
    Mac
    
        brew install ansible
    
    
    Linux 
  
        sudo apt-get install ansible
    
  * __sshpass__ - Unix package
  
    Mac 
    
        brew install https://raw.github.com/eugeneoden/homebrew/eca9de1/Library/Formula/sshpass.rb
    
    
    Linux 
    
        sudo apt-get install sshpass
    

  * __vagrant__ - http://www.vagrantup.com/
  * __VirtalBox__ - https://www.virtualbox.org/


## "Playbooks" to run

1. `create_instructor_user` - creates instructor user and adds SSH keys to all machines

    Example, 
        
        ansible-playbook --ask-pass -u root -i 168.192.0.1, create_instructor_user.yml
        ansible-playbook --ask-pass -u root -i hosts/class create_instructor_user.yml
  
    Results in: 
  
        username = instructor
        password = gdi
  
    For users whoese SSH keys were added the `authorized_keys` file, 
    
        ssh instructor@< ip_address > 

## License 
Copyright (c) 2014 Girl Develop It, CC BY-NC 3.0. 
