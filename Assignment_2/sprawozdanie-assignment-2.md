# Zaj�cia 02  
## Zestawienie �rodowiska  
1. Zainstaluj Docker w systemie linuksowym  
![](./screenshots/001.png)  
2. Zarejestruj si� w Docker Hub i zapoznaj z sugerowanymi obrazami  
![](./screenshots/002.png)  
3. Pobierz hello-world, busybox, ubuntu lub fedor�, mysql  
![](./screenshots/003.png)  
4. Uruchom busybox  
   - Poka� efekt uruchomienia kontenera  
![](./screenshots/004.png)  
   - Pod��cz si� do kontenera interaktywnie i wywo�aj numer wersji  
![](./screenshots/005.png)  
5. Uruchom "system w kontenerze"  
   - Zaprezentuj PID1 w kontenerze i procesy dockera na ho�cie  
![](./screenshots/007.png)  
![](./screenshots/006.png)  
   - Zaktualizuj pakiety  
![](./screenshots/008.png)  
   - Wyjd�  
Komenda "exit"  
6. Poka� uruchomione ( != "dzia�aj�ce" ) kontenery, wyczy�� je.  
![](./screenshots/009.png)  
![](./screenshots/010.png)  
7. Wyczy�� obrazy  
![](./screenshots/011.png)  

## Budowanie programu  
1. Znajd� projekt umo�liwiaj�cy �atwe wywo�anie test�w jednostkowych  
![](./screenshots/012.png)  
2. Przeprowad� budow�/konfiguracj� �rodowiska  
![](./screenshots/013.png)  
![](./screenshots/014.png)  
![](./screenshots/015.png)  
3. Uruchom testy  
![](./screenshots/016.png)  
4. Pon�w ten proces w kontenerze  
   - Wybierz i uruchom platform�  
Wybrano ubuntu:latest  
   - Zaopatrz j� w odpowiednie oprogramowanie wst�pne  
![](./screenshots/017.png)  
![](./screenshots/018.png)  
   - Sklonuj aplikacj�  
![](./screenshots/019.png)  
   - Skonfiguruj �rodowisko i uruchom build  
![](./screenshots/020.png)  
![](./screenshots/021.png)  
   - Uruchom testy  
![](./screenshots/022.png)  
5. Stw�rz Dockerfile, kt�ry ma to osi�gn��  
   - Na bazie platformowego obrazu...  
FROM ubuntu
   - ...doinstaluj wymagania wst�pne...  
RUN apt update && apt -y upgrade && apt install -y git dotnet-sdk-6.0  
   - ...sklonuj repozytorium...  
RUN git clone {repo URL}  
   - ...zbuduj kod  
RUN dotnet restore ./InventoryUniversity.sln  
RUN dotnet build ./InventoryUniversity.sln  
6. Zaprezentuj Dockerfile i jego zbudowanie  
![](./screenshots/023.png)  
![](./screenshots/024.png)  
![](./screenshots/025.png)  
![](./screenshots/026.png)  
7. Na bazie obrazu utworzonego poprzednim dockerfilem stw�rz kolejny, kt�ry b�dzie uruchamia� testy  
![](./screenshots/027.png)  
 	* Kontener pierwszy ma przeprowadza� wszystkie kroki a� do builda  
![](./screenshots/028.png)  
	* Kontener drugi ma bazowa� na pierwszym i wykonywa� testy  
![](./screenshots/029.png)  
## Runda bonusowa: kompozycja  
![](./screenshots/030.png)  
![](./screenshots/031.png)  
1. Zdefiniuj kompozycj�, kt�ra stworzy dwie us�ugi  
   - Pierwsz� na bazie dockerfile'a buduj�cego  
   - Drug� na bazie pierwszej  
![](./screenshots/032.png)  
2. Wdr� :)  
![](./screenshots/033.png)     