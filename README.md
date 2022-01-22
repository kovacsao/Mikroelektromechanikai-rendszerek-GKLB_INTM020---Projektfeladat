# Projekt1
## Micro sero motorok init
(Alkatrész lista a dokumentációban részletesen.)
A motorok és a PWM board megfelelő összekötése után a következő utasításokat szükséges elvégezni: 
`code`

## WS2812 RGB LED vezérlés elindítása:
```
sudo apt-get update
sudo apt-get install gcc make build-essential python-dev git scons swig
```
Ebbe az állományba:
```
sudo nano /etc/modprobe.d/snd-blacklist.conf
```
Illesszük be ezt:
```
blacklist snd_bcm2835
```
Mentés: CTRL + O  
Bezárás:CTRL + X

Szerkesztenünk szükséges a következő konfigurációs állományt:
```
sudo nano /boot/config.txt
```
Ha szükséges Ctrl + W a keresés
```
# Enable audio (loads snd_bcm2835)
dtparam=audio=on
```
Ki kell kommentelnünk #dtparam=audio=on

Majd újraindítjuk a rendszert
```
sudo reboot
```
Letöltjük a következő git könyvtárat:
```
git clone https://github.com/jgarff/rpi_ws281x
```
Ha ez megvan akkor:
```
cd rpi_ws281x/
sudo scons
```
Majd a következő helyre navigálunk:
```
cd python
```
Ahol kivitelezni tudjuk a telepítést:
```
sudo python3 setup.py build 
sudo python3 setup.py install 
sudo pip3 install adafruit-circuitpython-neopixel
```
Ezután megnyitjuk a következő állományt:
```
sudo nano examples/strandtest.py
```
Ahol inicializáljuk a LED_COUNT = led számot és a LED_PIN = PIN számot (amire csatlakoztattuk, PWM lábra)
Aztán mentés, bezárás majd mehet a teszt (Mentés: CTRL + O; Bezárás: CTRL + X)
```
sudo python3 examples/strandtest.py
```