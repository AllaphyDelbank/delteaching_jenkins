## Iniciar os bagulhos
### Atualizar pacotes e instalar os docker coisos

```
sudo apt-get update -y && sudo apt-get upgrade -y &&
sudo apt install docker.io -y &&
sudo apt install docker-compose-v2 -y
```
### Instanciar o Jenkins

```
sudo docker volume create jenkins_data && 
sudo chown -R 1000:1000 /var/lib/docker/volumes/jenkins_data/ &&
sudo docker run -d -it --restart always -u root -p 8080:8080 -p 50000:50000 -v /var/run/docker.sock:/var/run/docker.sock -v $(which docker):/usr/bin/docker -v jenkins_data:/var/jenkins_home --name jenkins jenkins/jenkins:latest
```

## Integrar com GitHub
### Na máquina host:
1. ```ssh-keygen -t rsa -b 4096 -C "{SeuEmail}"```
2. ```cat /root/.ssh/id_rsa.pub && cat /root/.ssh/id_rsa```
3. Adicionar public SSH no Github
4. Adicionar credenciais (private SSH) no Jenkins
5. ```ssh -T git@github.com```

Obs: Se não funcionar, clone o repositório

6. Adicionar webhook do Jenkins no GitHub 
'/github-webhook/'
7. Instalar plugin 'SSH Agent'
8. Alterar a estratégia de conexão com o Git ('Manage Jenkins' -> 'Security' -> 'Git Host Key Verification')*

#### Troubleshooting (desespero):
1. ```docker exec -it jenkins bash```
2. ```rm -rf /var/jenkins_home/workspace/{nomeDoWorkspace} && exit```
3. ```docker restart jenkins```
4. ```docker exec -it jenkins bash```
5. ```chown -R jenkins:jenkins /var/jenkins_home```
6. 
```docker cp ~/.ssh/id_rsa jenkins:/root/.ssh/id_rsa
docker cp ~/.ssh/id_rsa.pub jenkins:/root/.ssh/id_rsa.pub
docker cp ~/.ssh/known_hosts jenkins:/root/.ssh/known_hosts
exit 
```
7. ```docker exec -it jenkins bash```
8. 
```
chmod 600 /root/.ssh/id_rsa
chmod 644 /root/.ssh/id_rsa.pub
chmod 644 /root/.ssh/known_hosts
```
9. ```ssh -T git@github.com```

### Ferramenta massa para expôr a aplicação:
1. ```ssh -R 80:localhost:8080 localhost.run```

"# aula" 
