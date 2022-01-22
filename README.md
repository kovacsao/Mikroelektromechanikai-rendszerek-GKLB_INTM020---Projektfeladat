# Mikroelektromechanikai rendszerek (GKLB_INTM020) - Projektfeladat

## Robot felépítése
3D nyomtatott alkatrészekből felépített aktuátor, aminek elemei összeilleszthetők és belefér egy SG90-es servo motor

## PWM Board PCA 9685
![PCA9685](https://github.com/kovacsao/Mikroelektromechanikai-rendszerek-GKLB_INTM020---Projektfeladat/blob/main/K%C3%A9pek/3-3-5%2016%20csatorn%C3%A1s%20pwm-1.jpg)

![PCA9685](https://github.com/kovacsao/Mikroelektromechanikai-rendszerek-GKLB_INTM020---Projektfeladat/blob/main/K%C3%A9pek/pca9685_1.jpg)

## Micro sero motorok - SG90
![SG90](https://github.com/kovacsao/Mikroelektromechanikai-rendszerek-GKLB_INTM020---Projektfeladat/blob/main/K%C3%A9pek/sg90.jpg)
A motorok és a PWM board I2C inicializálása után használható. Ha nem áll rendelkezésre ez a board, akkor PWM szabályzás javasolt, viszont az ilyen GPIO lábak száma véges. 

## HC-SR04 Ultrahang szenzor beállítása:
![Ultrahang bekötés](https://github.com/kovacsao/Mikroelektromechanikai-rendszerek-GKLB_INTM020---Projektfeladat/blob/main/K%C3%A9pek/ultra1.PNG)

Ha nem akar működni, valószínűleg azért van mert ennek a szenzornak van beépített chip-es verziója is, amihez kevesebb ellenállást szükséges berakni a működéshez. (Kezdésnek 1K-t javaslom)

*http://www.malnasuli.hu/projektek/tavolsagmeres-hc-sr04-ultrahang-szenzorral/*

## Kijelző : D096-128x64 OLED
Az eszköz a helyes rákötés és I2C inicializálás után használható. 
```
i2cdetect -y 1
```
Az alkalmazott OLED kijelzők alapértelmezett címe: “0x3c”, így a megfelelő működés esetén a parancs kiadása után a következő képernyőkép jelenik meg:
![Kijelzo]()

Amennyiben a Pi a megfelelő címen érzékeli a rákötött kijelzőt, következhet a szükséges Python library telepítése. Ehhez egy terminál ablakban gépeljük be a következő utasításokat:

Telepítsük az Adafruit git repository-t:
```
sudo apt install -y git
git clone https://github.com/adafruit/Adafruit_Python_SSD1306.git
```
Navigálás a library könyvtárába:
```
cd Adafruit_Python_SSD1306
```
Telepítsük a library-t:
```
sudo python3 setup.py install
```
Ha minden rendben lezajlott, akkor ezek után a kijelző már programozható. Tesztelésként töltsük be, vagy gépeljük be az alábbi programot:
```
    #szükséges library-k importálása
    import time
    import Adafruit_GPIO.SPI as SPI
    import Adafruit_SSD1306
    from PIL import Image
    from PIL import ImageDraw
    from PIL import ImageFont
    # Raspberry Pi pin configuration:
    RST = None     # ez a tüske nincs használatban
    # 128x32 display hardveres I2C:
    disp = Adafruit_SSD1306.SSD1306_128_32(rst=RST)
    # library inicializálás
    disp.begin()
    # display törlése
    disp.clear()
    disp.display()
    # üres image betöltése
    # '1'-es érték az 1-bit-es színmód beállítására
    width = disp.width
    height = disp.height
    image = Image.new('1', (width, height))
    # Get drawing object to draw on image.
    draw = ImageDraw.Draw(image)
    # Draw a black filled box to clear the image.
    draw.rectangle((0,0,width,height), outline=0, fill=0)
    # állandók definiálása az átméretezés könnyítésére 
    padding = -2
    top = padding
    bottom = height-padding
    x = 0
    # fontkészlet betöltése
    font = ImageFont.load_default()
    # font = ImageFont.truetype('Netron.ttf', 8)
    draw.text((x, top+0),"     Hello World", font=font, fill=255)
    draw.text((x, top+8),"      malnapc.hu", font=font, fill=255)
    draw.text((x, top+16),"     malnasuli.hu", font=font, fill=255)
    draw.text((x, top+25)," ********************", font=font, fill=255)
    # image megjelenítése
    disp.image(image)
    disp.display()
```

[Forrás](https://www.malnasuli.hu/raspberry_pi/i2c-s-kijelzok-raspberry-pi-hez-1-oled-kijelzo/)

## I2C 
```
sudo apt-get install -y python-smbus
sudo apt-get install -y i2c-tools
```
A telepítések után szükségünk van a konfigurációs beállítások ellenőrzésére:
```
sudo raspi-config
```
Interfacing Options/Advanced/I2C
...és itt engedélyezés...
Majd reboot
```
sudo reboot
```

Ahhoz, hogy az eszközöket ellenőrizni tudjuk, a következő paranccsal tudunk egy áttekintő képet varázsolni:
```
sudo i2cdetect -y 1
```

## Email küldés
Az email küldéshez szükség van egy alacsony biztonságra beállított email címre. Gmail platformon egy külön erre a célra létrehozott email fiók ajánlott. A beállítást ezen a [linken](https://support.google.com/accounts/answer/6010255?hl=en&authuser=1&pli=1#zippy=%2Cif-less-secure-app-access-is-on-for-your-account) keresztül lehet elérni. 

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