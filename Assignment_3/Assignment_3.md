## Assignment 3
## Pipeline, Jenkins, izolacja etap�w

### Przygotowanie
* Zapoznaj si� z instrukcj� instalacji Jenkinsa: https://www.jenkins.io/doc/book/installing/docker/
  * Uruchom obraz Dockera kt�ry eksponuje �rodowisko zagnie�d�one  
Wyczy�ci�em `DOCKER_TLS_CERTDIR=""` w celu u�ycia HTTP a nie HTTPS (z nim by�y problemy).  
```
sudo docker run \
  --name jenkins-docker \
  --rm \
  --detach \
  --privileged \
  --network jenkins \
  --network-alias docker \
  --env DOCKER_TLS_CERTDIR="" \
  --volume jenkins-docker-certs:/certs/client \
  --volume jenkins-data:/var/jenkins_home \
  --publish 2376:2376 \
  docker:dind \
  --storage-driver overlay2
```
![](./screenshots/001.png)  
  * Przygotuj obraz blueocean na podstawie obrazu Jenkinsa  
Dockerfile do build'u:  
```dockerfile
FROM jenkins/jenkins:2.375.1
USER root
RUN apt-get update && apt-get install -y lsb-release
RUN curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc \
  https://download.docker.com/linux/debian/gpg
RUN echo "deb [arch=$(dpkg --print-architecture) \
  signed-by=/usr/share/keyrings/docker-archive-keyring.asc] \
  https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list
RUN apt-get update && apt-get install -y docker-ce-cli
USER jenkins
RUN jenkins-plugin-cli --plugins "blueocean:1.26.0 docker-workflow:563.vd5d2e5c4007f"
```
  * Uruchom Blueocean  
Czyszcz� `DOCKER_HOST=""`, `DOCKER_TLS_VERIFY=""` i `DOCKER_CERT_PATH=""` w celu u�ycia HTTP  

```
docker run \
  --name jenkins-blueocean \
  --restart=on-failure \
  --detach \
  --network jenkins \
  --env DOCKER_HOST="" \
  --env DOCKER_CERT_PATH="" \
  --env DOCKER_TLS_VERIFY="" \
  --publish 8080:8080 \
  --publish 50000:50000 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  myjenkins-blueocean:2.375.1-1 
```
  * Zaloguj si� i skonfiguruj Jenkins  
![](./screenshots/002.png)  
Skonfigurowa�em po��czenie z kontenerem dind:  
![](./screenshots/021.png)  
  
### Uruchomienie 
* Konfiguracja wst�pna i pierwsze uruchomienie  
  * Utw�rz projekt, kt�ry wy�wietla uname  
Przechodzimy do `New Item`.  
![](./screenshots/003.png)  
Wpisujemy nazw� projektu i wybieramy typ `Freestyle project`:  
![](./screenshots/004.png)  
W celu konfiguracji przechodzimy do kroku `Build Steps` (na screenie jest ma�y b��d) i wybieramy Build step `Execute shell`:    
![](./screenshots/005.png)  
W oknie input boxie wpisujemy komend� kt�ra ma si� wykona�, w tym wypadku `uname -a`:  
![](./screenshots/006.png)  
W celu uruchomienia klikamy `Build Now`:  
![](./screenshots/007.png)  
Efektem jest uruchomienie build'u, kt�re s� numerowane. Przejd�my do wykonanego build'u:  
![](./screenshots/008.png)
Nast�pnie aby zobaczy� wynik komendy przechodzimy do `Console Output`:  
![](./screenshots/009.png)  
  * Utw�rz projekt, kt�ry zwraca b��d, gdy... godzina jest nieparzysta  
W tym celu tworzymy projekt jak poprzednim razem, zmieniamy jedynie wykonywany skrypt:  
![](./screenshots/010.png)  
Dodatkowo doda�em Schedule `H/30 * * * *` w celu uruchamiania build'u co 30 min:  
![](./screenshots/011.png)  
Wynik build'�w:  
![](./screenshots/011a.png)  
* Utw�rz "prawdziwy" projekt, kt�ry:  
  * klonuje nasze repozytorium  
  * przechodzi na osobist� ga���  
Powy�sze podpunkty realizujemy poprzez opcj� w konfiguracji projektu `Source Code Management` gdzie wstawiamy URL repozytorium oraz jego ga���:  
![](./screenshots/012.png)  
  * buduje obrazy z dockerfiles i/lub komponuje via docker-compose  
Skrypt odpowiadaj�cy za budowanie i testy:  
![](./screenshots/013.png)  
Wynik builda w konsoli:  
![](./screenshots/014.png)  
  
### Pipeline
Diagram aktywno�ci:  
![](./screenshots/015.jpg)  
* Checkout - klonuje swoje repozytorium `https://github.com/szymongamza/DevOpsJenkinsTry`  
* Build - uruchamia kontener kt�ry buduje moj� aplikacj�. Jako obrazu bazowego u�ywa `mcr.microsoft.com/dotnet/sdk:7.0`  
* Test - uruchamia kontener kt�ry testuje moj� aplikacj�. Jako obrazu bazowego u�ywa obraz buduj�cy z poprzedniego stage'a  
* Publish - buduje moj� aplikacj� pod kilka platform w wersji `--self-contained` jako artefakty, w celu �atwego uruchomienia starych wersji aplikacji bez martwienia si� o dependencje. W celu �atwego wdra�ania, buduj� dodatkowy obraz posiadaj�cy aplikacj� w formie `framework-dependent binary` oparty na obrazie runtime `mcr.microsoft.com/dotnet/aspnet:7.0`, pozwala to na dowoln� cross-platformowo�� i �atwy deploy.  
* Deploy - przes�anie obrazu do docker hub'a.  
Diagram wdro�eniowy:  
![](./screenshots/016.jpg)  
Wdro�eniem u mnie jest, przes�anie obrazu do Docker Hub'a z �rodowiska Jenkins.  
Tak wygl�da konfiguracja Pipeline'a w Jenkins:  
![](./screenshots/017.png)  
Tak wygl�da repozytorium z plikami `Jenkinsfile` oraz przydatnymi `Dockerfile`:  
![](./screenshots/018.png)  
Jenkinsfile:  
```Jenkinsfile
pipeline {
    agent any
    environment{
        DOCKERHUB_CREDENTIALS=credentials('851b9618-d12c-4037-97ab-61baa8600146')
    }
    stages {
        stage('Checkout') {
            steps {
                git url:'https://github.com/szymongamza/DevOpsJenkinsTry.git', branch: 'main'
            }
        }
        stage('Build') {
            steps {
                sh 'docker build -f Dockerfile.b  -t image_build .'
            }
        }
        stage('Test') {
            steps {
                sh 'docker build -f Dockerfile.t -t image_test .'
            }
        }
        stage('Publish') {
            steps {
                sh """
                    docker build -f Dockerfile.pub -t image_publish .
                    docker run -d --name temp_cont --tty image_publish
                    docker cp temp_cont:/app/publish/ ./artifacts
                    docker rm -f temp_cont
                """
                zip zipFile: './artifacts/win-x64.zip', archive: false, dir: './artifacts/win-x64'
                tar file: './artifacts/linux-x64.tar', archive: false, dir: './artifacts/linux-x64'
                tar file: './artifacts/osx-x64.tar', archive: false, dir: './artifacts/osx-x64'
                archiveArtifacts artifacts: 'artifacts/win-x64.zip', fingerprint: true
                archiveArtifacts artifacts: 'artifacts/linux-x64.tar', fingerprint: true
                archiveArtifacts artifacts: 'artifacts/osx-x64.tar', fingerprint: true
                sh 'docker build -f Dockerfile.dep -t szymongamza/todolist:latest .'
                sh 'rm -r ./artifacts'
            }
        }
        stage('Deploy') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push szymongamza/todolist:latest'
            }
        }
    }
    post {
		always {
			sh 'docker logout'
		}
	}
}
```
Dockerfile.b:  
```Dockerfile.b
FROM mcr.microsoft.com/dotnet/sdk:7.0
COPY . .
RUN ls
RUN dotnet restore ToDoListAPI.sln
RUN dotnet build ToDoListAPI.sln
```
Dockerfile.t:  
```Dockerfile.t
FROM image_build
RUN dotnet test ToDoListAPI.sln
```
Dockerfile.pub:  
```Dockerfile.pub
FROM image_test
RUN dotnet publish "./ToDoListAPI.sln" -c Release -o /app/publish/dockerize
RUN dotnet publish -r win-x64 --self-contained true "./ToDoListAPI.sln" -c Release -o /app/publish/win-x64
RUN dotnet publish -r linux-x64 --self-contained true "./ToDoListAPI.sln" -c Release -o /app/publish/linux-x64
RUN dotnet publish -r osx-x64 --self-contained true "./ToDoListAPI.sln" -c Release -o /app/publish/osx-x64
```
Dockerfile.dep:  
```Dockerfile.dep
FROM mcr.microsoft.com/dotnet/aspnet:7.0
EXPOSE 80
WORKDIR /app
COPY ./artifacts/dockerize/ .
ENTRYPOINT ["dotnet", "ToDoListAPI.dll"]
```
Tak wygl�da uruchomiony build w pipeline'ie. Aktualnie m�j build pozostawia 3 artefakty (opr�cz loga), `linux-x64.tar`,`osx-x64.tar` oraz `win-x64.zip`. Zadba�em aby rozszerzenie archiwum odpowiada�o platformie:  
![](./screenshots/019.png)  
W pipeline u�y�em step�w u�atwiaj�cych archiwizowanie plik�w, nale�� one do pluginu `Pipeline Utility Steps`:  
```
zip zipFile: './artifacts/win-x64.zip', archive: false, dir: './artifacts/win-x64'
tar file: './artifacts/linux-x64.tar', archive: false, dir: './artifacts/linux-x64'
tar file: './artifacts/osx-x64.tar', archive: false, dir: './artifacts/osx-x64'
```
![](./screenshots/020.png)  
Efekt stage'u Deploy:  
![](./screenshots/022.png)  
W pipeline w celu Deploy'u musz� zalogowa� si� do DockerHub -> Doda�em po�wiadczenia do Jenkins:  
![](./screenshots/023.png)  
![](./screenshots/024.png)  
