su — Switch User
su completely switches you to another user account.


# Switch to root user
su

# Switch to specific user
su kiwoly
su ubuntu
su postgres

# Switch to root and keep current environment
su -


sudo — Super User Do
sudo runs a single command as root WITHOUT switching users. After the command finishes you are still yourself.


# Run one command as root
sudo apt update
sudo systemctl restart nginx
sudo chmod 400 key.pem

# Open a root shell (like su -)
sudo -i
sudo su -


su                    sudo
│                     │
│ switches you        │ runs ONE command
│ to another user     │ as root then
│ completely          │ returns to you
│                     │
│ stays root          │ temporary root
│ until you exit      │ for one command