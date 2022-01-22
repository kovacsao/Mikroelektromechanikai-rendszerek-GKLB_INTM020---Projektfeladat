# Mikroelektromechanikai rendszerek (GKLB_INTM020) - Projektfeladat
## Robot felépítése
Nyomtatott alkatrészekből felépített aktuátor, aminek elemei összeilleszthetők
## PWM Board PCA 9685
![PCA9685](https://github.com/kovacsao/Mikroelektromechanikai-rendszerek-GKLB_INTM020---Projektfeladat/blob/main/K%C3%A9pek/3-3-5%2016%20csatorn%C3%A1s%20pwm-1.jpg)
![PCA9685](https://github.com/kovacsao/Mikroelektromechanikai-rendszerek-GKLB_INTM020---Projektfeladat/blob/main/K%C3%A9pek/pca9685_1.jpg)
## Micro sero motorok - SG90
![SG90](https://github.com/kovacsao/Mikroelektromechanikai-rendszerek-GKLB_INTM020---Projektfeladat/blob/main/K%C3%A9pek/sg90.jpg)
A motorok és a PWM board I2C inicializálása után használható. Ha nem áll rendelkezésre ez a board, akkor PWM szabályzás javasolt, viszont az ilyen GPIO lábak száma véges. 
`code`

## HC-SR04 Ultrahang szenzor beállítása:
![Ultrahang bekötés](https://github.com/kovacsao/Mikroelektromechanikai-rendszerek-GKLB_INTM020---Projektfeladat/blob/main/K%C3%A9pek/ultra1.PNG)

Ha nem akar működni, valószínűleg azért van mert ennek a szenzornak van beépített chip-es verziója is, amihez kevesebb ellenállást szükséges berakni a működéshez. (Kezdésnek 1K-t javaslom)

*http://www.malnasuli.hu/projektek/tavolsagmeres-hc-sr04-ultrahang-szenzorral/*


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
Ahol inicializáljuk a LED_COUNT = led (darab)számot és a LED_PIN = PIN számot (amire csatlakoztattuk, PWM lábra)
Aztán mentés, bezárás majd mehet a teszt (Mentés: CTRL + O; Bezárás: CTRL + X)
```
sudo python3 examples/strandtest.py
```