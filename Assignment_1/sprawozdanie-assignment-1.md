# Sprawozdanie Assignment-1
### 2022-12-10
---
1. Wyka� mo�liwo�� komunikacji ze �rodowiskiem linuksowym (pow�oka oraz przesy�anie plik�w)  
![](./screenshots/1.png)  
2. Zainstaluj klienta Git i obs�ug� kluczy SSH  
![](./screenshots/2.png)  
3. Sklonuj repozytorium https://github.com/InzynieriaOprogramowaniaAGH/MDO2023 za pomoc� HTTPS  
![](./screenshots/3.png)  
4. Upewnij si� w kwestii dost�pu do repozytorium jako uczestnik i sklonuj je za pomoc� utworzonego klucza SSH  
   - Utw�rz dwa klucze SSH, inne ni� RSA, w tym co najmniej jeden zabezpieczony has�em  
![](./screenshots/4.1.png)  
![](./screenshots/4.2.png)  
   - Skonfiguruj klucz SSH jako metod� dost�pu  
![](./screenshots/4.3.png)  
![](./screenshots/4.4.png)  
   - Sklonuj repozytorium z wykorzystaniem protoko�u SSH  
![](./screenshots/4.5.png)  
5. Prze��cz si� na ga��� swojej grupy  
![](./screenshots/5.png)  
6. Utw�rz ga��� o nazwie "inicja�y & nr indeksu" np. ```KD232144```  
![](./screenshots/6.png)  
7. Rozpocznij prac� na nowej ga��zi  
![](./screenshots/7.1.png)  
   - W katalogu w�a�ciwym dla grupy utw�rz nowy katalog, tak�e o nazwie "inicja�y & nr indeksu" np. ```KD232144```  
   - W nowym katalogu dodaj plik ze sprawozdaniem  
   - Dodaj zrzuty ekranu  
![](./screenshots/7.2.png)  
   - Wy�lij zmiany do zdalnego �r�d�a  
![](./screenshots/7.3.png)  
![](./screenshots/7.4.png)  
   - Spr�buj wci�gn�� swoj� ga��� do ga��zi grupowej  
![](./screenshots/7.5.png)  
   - Zaktualizuj sprawozdanie i zrzuty o ten krok i wy�lij aktualizacj� do zdalnego �r�d�a (na swojej ga��zi)  
![](./screenshots/7.6.png)  
   - Oznacz tagiem ostatni commit i wypchnij go na zdaln� ga���  
![](./screenshots/7.7.png)  
   - Ustal hook, kt�ry b�dzie sprawdza�, czy wiadomo�� z commitem zawiera nazw� przedmiotu  
![](./screenshots/7.8.png)  
![](./screenshots/7.8.1.png)  
   - W jaki spos�b stworzy� hook, kt�ry b�dzie *ustawia�* prefiks wiadomo�ci commitu tak, by mia� nazw� przedmiotu?  
![](./screenshots/7.9.png)  
![](./screenshots/7.9.1.png)  
![](./screenshots/7.9.2.png)  
### Weryfikacja dzia�ania �rodowiska konteneryzacji  
1. Rozpocznij przygotowanie �rodowiska Dockerowego  
    * zapewnij dost�p do maszyny wirtualnej przez zdalny terminal (nie "przez okienko")  
    * je�eli nie jest stosowane VM (np. WSL, Mac, natywny linux), wyka� ten fakt **dok�adnie**  
    * zainstaluj �rodowisko dockerowe w stosowanym systemie operacyjnym  
2. Dzia�anie �rodowiska  
    * wyka�, �e �rodowisko dockerowe jest uruchomione i dzia�a (z definicji)  
    * wyka� dzia�anie z spos�b praktyczny (z w�asno�ci):  
      * pobierz obraz dystrybucji linuksowej i uruchom go  
      * wy�wietl jego numer wersji  
![](./screenshots/8.png)  
5. Za�� konto na Docker Hub lub zaloguj si� do ju� posiadanego. Zadbaj o 2FA.  
![](./screenshots/9.png)  