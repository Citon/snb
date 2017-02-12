SNB - Shake N' Bake Interactive Template Filler
-----------------------------------------------

Somewhere between manually typing everything and full automation
exists the realm of the quick and the dirty.  Getting the job done
so you can go have a nice family dinner.  If you just need to
fill out a Jinja2 template real fast and all you have is a keyboard,
snb might be for you.

Invented to avoid asking other admins to edit a fiddly text config
file by hand, and to avoid needing a full blown config management
system online when all you really need to do is punch out 14 spots
in a static template then paste it into a serial console.  

REQUIREMENTS
------------

* Python 3.
* Some sort of OS.  Probably Linux or BSD would be good.
* Courage.

INSTALLATION
-----------

Funny.  Just run snb.  Or run python3 snb.  Ok, here is how to install
it if you must know:

	sudo cp snb /usr/local/bin
	sudo chmod 755 /usr/local/bin/snb

USAGE
-----

Takes Jinja2 template as STDIN, asks the user to enter anything it needs
to fill out, then kicks out a filled out template on STDOUT.

Let's say you had a file named "shake.conf" that looked like this:

	conf term
	hostname {{ sitename }}wan

	int gi0/0.10
	desc DATA-VL10
	enc dot1q 10
	ip address 10.{{ sitenumber }}.10.1 255.255.255.0

	int gi0/0.20
	desc VOICE-VL20
	enc dot1q 20
	ip address 10.{{ sitenumber }}.20.1 255.255.255.0

	int gi1/0
	desc WAN
	ip address {{ wan_ip }} 255.255.255.252
	
	ip route 0.0.0.0 0.0.0.0 {{ wan_gateway }}
	end
	wr mem

If you wanted to interactively turn it into a file named "baked-site-213.conf"
just do:

	cat shake.conf | snb > baked-site-213.conf

You would then be prompted for the following:
* sitename - A name for the site.  Let's call it 'baked'
* sitenumber - Assuming some sort of simple number to site mapping.  Let's
               say this is site 213
* wan_ip - The WAN side IP address.  Let's say 192.168.14.14.
* wan_gateway - The WAN gateway IP.  Let's say 192.168.14.13.

The resulting output would be:

	conf term
	hostname bakedwan

	int gi0/0.10
	desc DATA-VL10
	enc dot1q 10
	ip address 10.213.10.1 255.255.255.0

	int gi0/0.20
	desc VOICE-VL20
	enc dot1q 20
	ip address 10.213.20.1 255.255.255.0

	int gi1/0
	desc WAN
	ip address 192.168.14.14 255.255.255.252
	
	ip route 0.0.0.0 0.0.0.0 192.168.14.13
	end
	wr mem

Stupid simple, huh?

There is one "feature" - If your template variable ends in "password"
the input will be fed through UNIX crypt using SHA512.  (Type 6).  You
can use this feature on Linux, BSD, JUNOS, and anything else that can
take a salted crypt output.  Example (from a JUNOS setup):

	set system root-authentication encrypted-password "{{ root_password }}"

When fed through, after entering your root_password value, might produce:

	set system root-authentication encrypted-password "$6$THum5Wu3c7GguJbo$efPQeMaizqNcr8V2sp0lj/q5Vy3aZMXtptHmy/neFvEIZICE/SWEYzVH3wVBMZJ0iIT0k1cgzbVNZq7DqVJ2a0"


DISCLAIMER
----------

This product is not endorsed by KRAFT, makers of Shake 'N Bake, nor by Sony
Pictures, distributors of Talladega Nights.  Heck, I barely endorse it.

Also, this README is longer than the script.  So there is that.
