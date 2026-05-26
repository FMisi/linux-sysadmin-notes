## Desktop (GNOME)

sudo dnf install epel-release -y
sudo dnf groupinstall "Server with GUI" -y
sudo systemctl set-default graphical.target
reboot
