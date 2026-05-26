# Brat Robotic Autonom pentru X si 0 (Tic-Tac-Toe)

Acest proiect reprezinta un brat robotic de tip Pen Plotter cu 4 grade de libertate (4-DOF), capabil sa joace fizic X si 0 impotriva unui om, direct pe o foaie de hartie. Sistemul utilizeaza Computer Vision pentru a urmari starea tablei de joc si algoritmul Minimax pentru a lua decizii imbatabile.

[Demonstratie Video YouTube](https://youtu.be/3SBCODZh4pE?si=cWG9eyGfCfojJWsp)

## Arhitectura Sistemului (Master-Slave)

Sistemul este impartit in doua module principale care comunica bidirectional prin protocolul UART over USB:

### 1. Master (Laptop - Python)
Actioneaza ca un Industrial PC (IPC), preluand sarcinile grele de procesare:
- **Computer Vision (OpenCV):** Extrage grila de joc, filtreaza zgomotul optic (Adaptive Thresholding + operatii morfologice) si clasifica formele (X sau O) folosind o analiza stricta de densitate pe o grila interna 3x3.
- **Inteligenta Artificiala (Minimax):** Evalueaza arborele de joc in timp real si calculeaza mutarea perfecta, garantand castigul sau remiza.
- **Control Cinematic Optimizat:** Rezolva jocurile mecanice ale printului 3D folosind o calibrare empirica (Teach Pendant) si previne distrugerea structurii aplicand rampe de accelerare software pentru miscari fluide.

### 2. Slave (Arduino Uno - Bare-Metal C/C++)
Se ocupa strict de executia fizica, fiind programat la nivel de registri:
- **Cod Bare-Metal (fara librarii):** Manipulare directa a registrilor microcontrolerului ATmega328P.
- **Comunicatie Non-Blocanta:** Utilizeaza intreruperi hardware (`ISR`) pentru a citi unghiurile trimise de PC in fundal, fara a bloca procesorul.
- **Software PWM (Timer1):** Genereaza semnale PWM precise la nivel de 0.5 microsecunde pentru cele 5 servomotoare, asigurand o acuratete net superioara librariilor standard.

## Hardware Folosit

- 1 x Placa de dezvoltare compatibila Arduino Uno
- 1 x Camera web USB
- 1 x Arduino Sensor Shield V5.0 (cu jumper-ul SEL eliminat)
- 4 x Servomotoare MG995 (Baza, Umar Stang, Umar Drept oglindit, Cot)
- 1 x Micro-servomotor MG90 (Incheietura / sistem ridicare marker)
- 1 x Sursa de alimentare 5V 5A DC (Putere independenta pentru motoare)
- Componente structurale printate 3D


## Cum se ruleaza

1. **Hardware:** Conectati sursa externa de 5V/5A la Sensor Shield si camera web la laptop.
2. **Firmware Arduino:** Incarcati codul din folderul `arduino_firmware` pe placa Arduino Uno. Robotul se va ridica in pozitia sigura de repaus.
3. **Software Python:**
   - Instalati dependintele: `pip install -r requirements.txt`
   - Asigurati-va ca portul serial in `X&0.py` este corect (ex: `/dev/ttyUSB0` sau `COM3`).
   - Rulati scriptul principal: `python X&0.py`
4. **Jocul:** Plasati tabla de joc in fata robotului. Asteptati ca robotul sa faca prima mutare (X). Desenati 'O' in celula dorita si retrageti mana. Camera va detecta stabilitatea formei desenate, iar robotul va raspunde automat.