docker-postfix-mailtrap
=======================

run postfix in a Docker container to forward all incoming mails to a single mailbox on the Docker host. Intended for developers of applications that send email to arbitrary addresses.

## Installation
1. Pull image

	```
	sudo docker pull boomfish/postfix-mailtrap
	```

## Usage
1. Create and run a postfix container that maps its mail spool to the host's mail spool

	```
	sudo docker run \
			-e mynetworks="192.168.1.0/24" -e mailbox_username=alan -e mailbox_uid=1001 \
			-v /var/mail:/var/mail --name mailtrap -d boomfish/postfix-mailtrap
	```
2. Create application containers that link to the mailtrap container for their outgoing SMTP service

	```
	sudo docker run -l mailtrap:mailhost -d myapp
	```

3. When an application container sends email via mailhost, check for it in the specified mailbox (in this case alan) on the Docker host using your favourite mail reader.

## Notes

+ The mailbox defaults to vagrant (uid 1000) which is what you usually want if you're running Docker under Vagrant.
+ Due to anti-relay protections postfix will reject unauthenticated emails from hosts that are not in any of "mynetworks" subnets, so you'll need to set "mynetworks" to the subnet range used by your Docker containers. The default for "mynetworks" includes all class B subnets (which is what Docker uses for local container interfaces).
+ If you wish to use SASL SMTP authentication read the information on [docker-postfix](https://github.com/catatnight/docker-postfix).

## License

This code is available under the [MIT License](LICENSE.txt).

## Acknowledgements

This Docker image is built over the [docker-postfix](https://github.com/catatnight/docker-postfix) image by [Elliot Ye](https://github.com/catatnight). The `install.sh` script is based largely off the same script from that repository.
