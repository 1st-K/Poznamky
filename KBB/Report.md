

# Report - Pan Učitel

Členové - Oliver Sobčák
Kdy/kde: 23. 3. 2026, SSPŠ

Konfigurace SSH - Haxagon Úloha



Dělal jsem SSH úlohu a zde je postup replikace:

### Postup:
	1) 
ssh root@179.5.5.5 -i /tmp/key/id_ed25519
cp /tmp/key/id_ed25519 /home/student/.ssh/
ssh root@179.5.5.5
cat flag
	2)
	ssh-copy-id -i /tmp/keys-to-setup/id_ed25519.pub ukol2@179.5.5.6
	3)
	mkdir .ssh
	cd .ssh
	'echo "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJLi8KPVt8cscjSA5K5BOIfc22LswB6yi2tV+AIAjCYd setup-key" > authorized_keys'
	chmod 700 ~/.ssh