# kubernetes-commands
KUBERNETS

o Kubernetes é utilizado para criar um cluster
um cluster é um ou mais container para disponibilizar que forma uma aplicação
dentro do cluster as maquinas se conhecem mas existe um "gerente"
o gerente se chama master (mestre em português)
no cluster também tem um ou mais node para rodar a aplicação
o Kubernetes tem o seu foco em aplicações que rodam usando containers
o Kubernetes é uma plataforma open-source desenvolvida pela Google

-Os pods são os objetos mais básicos existentes no Kubernetes e dessa forma, eles não oferecem alguns recursos, entre eles o de informar o estado desejado da nossa aplicação para o Kubernetes gerenciar. Dessa forma, quando trabalhamos com o Kubernetes, realizamos a abstração desses objetos Pods em objetos que oferecem mais recursos, como por exemplo o objeto Deployment que é capaz de adicionar o estado desejado da aplicação para o Kubernetes gerenciar

para testar o kubernetes localmente usa-se o minikube
para rodar o cluster usa-se minikube start
para parar o cluster: minikube stop
para limpar e apagar: minikube delete
minikube usa alguma virtualização por baixo dos panos como virtualbox ou vmware
para controlar o cluster usa-se kubectl
o menor objeto de deploy é o Pod
um Pod é o objeto que define como rodar o container (image, network, volumes, ports)
um Pod sozinho não garante o estado desejado
o comando kubectl create é usado para criar um Pod ou Deployment no cluster
o Deployment se baseia no Pod e garante o estado desejado
para receber infos sobre os Pods ou Deployments usamos kubectl get pods ou kubectl get deployments

Serviços
	Existem 3 tipo de serviços
		ClusterIP (padrão): para acesso interno apenas (os clientes internos enviam solicitações para um endereço IP interno estável.)
		NodePort: Expoe uma porta para acesso externo do seviço (os clientes enviam solicitações para o endereço IP de um nó em um ou mais valores de nodePort especificados pelo serviço.)
		LoadBalancer: os clientes enviam solicitações para o endereço IP de um balanceador de carga de rede.

## COMANDOS

kubectl
	sempre usaremos, para se comunicar como cluster

create
	para criar um pod conforme seu arquivo
	ex.: kubectl create -f aplicacao.yaml
	
	-f
		especificar o nome do arquivo yaml

appy
	parecido com o create, mas aqui aplica, alterando
	ex.: kubectl appy -f servico.yaml

	--namespace
		definindo namespace em que objeto será criado 
		ex.: kubectl apply -f backend-user-service.yaml --namespace staging
			ou
				kubectl apply -f backend-user-service.yaml -n staging

get
	trazer os pods
	
	pods
		traz os pods
		ex.: kubectl get pods

	all --all-namespaces
		traz informações de todos os namespaces

	watch kubectl get all --all-namespaces
		fica executando o comando a cada 2 segundos 

	<tipo>/<nomeObjeto> -o yaml
		para trazer a descrição do arquivo yaml 
		ex.: kubectl get pod/kubernetes-dashboard-7d75c474bb-xfzvp -n kube-system -o yaml

	secret
		traz objeto do tipo secrect, que guarda informações de senhas/token de autenticação
		ex.: kubectl get secret kubeadmin-token-5w7c2 -n kube-system

	ns ou namespaces
		para trazer os namespaces
		ex.: kubectl get ns

	pv,pvc
		para trazer os persistentVolume e persistentVolumeClaim
		ex.: kubectl get pv,pvc -n devops

logs -f
	para seguir log de um pod
	ex.: kubectl logs -f pod/backend-user-6b8759778f-2dnbq -n staging		

delete

	pods
		deleta o pod pelo nome
		ex.: kubectl delete pods aplicacao

	<tipoObjeto>/<nomeObjeto>
		e.: kubectl delete service/kubernetes-dashboard -n kube-system
		
describe
	mostra as informações dos pods
	
	grep (findstr no powershel)
		para filtrar uma informação
		ex.: kubectl describe pods | grep IP

	-n <nameSpace>
		para informar qual namespace o objeto está
		ex.: kubectl pod/kubernetes-dashboard-7d75c474bb-xfzvp -n kube-system

edit
	para editar um objeto diretamente
	ex.: kubectl edit svc staging-frontend -n staging


cluster-info
	mostra informações de execução do servidor adm do kubernetes

	caso der o erro: 

	execute os comandos: "The connection to the server localhost:8080 was refused - did you specify the right host or port?"
		sudo cp /etc/kubernetes/admin.conf $HOME/
		sudo chown $(id -u):$(id -g) $HOME/admin.conf
		export KUBECONFIG=$HOME/admin.conf

Podemos ver os pod's do CoreDNS em execução:
kubectl get pods --namespace=kube-system -l k8s-app=kube-dns

E podemos também subir um pod 'standalone':
kubectl create -f https://k8s.io/examples/admin/dns/busybox.yaml

E com ele consultar os servidores de DNS:
kubectl exec -ti busybox -- nslookup kubernetes.default

configMap
	Configurações de mapeamento para ambientes

	get cm --all-namespaces
		traz todos os confgmaps 
		ex.:  kubectl get cm --all-namespaces	

	appy
		para crair um configMap não se define o namespaces, pois será criado conforme descrito no arquivo
		ex.:kubectl apply -f configmaps.yaml		

secret
	Configurações de variáveis encriptografadas

	get secret -n <namespace>					
		para trazer as secrets para um namespace
		ex.: kubectl get secret -n prod

	appy
		para crair uma secret não se define o namespaces, pois será criado conforme descrito no arquivo
		ex.: kubectl apply -f secrets.yaml

serviceAcoount
	Configuraçóes de acesso para os serviços do kubernete

	describe sa kubeadmin -n kube-system
		para trazer as serviceAcoount conforme namespace
		ex.: kubectl describe sa kubeadmin -n kube-system

	kubectl get sa -n kube-system
		traz os sa de um namespace

	apply
		para crair sa não se define o namespaces, pois será criado conforme descrito no arquivo
		ex.: kubectl apply -f service-acoount.yaml

Ingress
	Responsável por fazer o mapeamento das URLs/IPs e portas

	Para listar mapeamentos ingress
		kubectl get ing -n kube-system	

	Para listar detalhes do mapeamento ingress
		kubectl describe ing traefik-web-ui -n kube-system

	Para todos os ingress de um namespace
		kubectl describe ing -n staging

patch
	recurso para alterar uma configuração de um objeto, aplicando um patch

	1. primeiro se cria um arquivo yaml com a alteração
		ex.: incluindo propriedade serviceAccountName nas especificações de um deployment
		spec:
  			template:
    			spec:
      				serviceAccountName: tiller

    2. aplicar o patch a um objeto
    	kubectl patch <tipoObjeto> <nomeObjeto> -n <namespace> --patch "$(cat patch.yaml)"
    	ex.: kubectl patch deployment tiller-deploy -n kube-system --patch "$(cat tiller-patch.yaml)"

proxy
	acessar um serviço de um pod interno, liberando uma url para conexão externa
	e.: kubectl proxy

expose
	para expor um serviço
		ex.: kubectl expose deployment kubernetes-dashboard --name=kubernetes-dashboard-nodeport --port=443 --target-port=8443 --type=NodePort -n kube-system	

kubectl version --short
	para ver a versão o kubernetes

OUTROS COMANDOS UTEIS

listar arquivo de configurações de acesso para dashboard, para geração do token
	kubectl get secret kubeadmin-token-5w7c2 -n kube-system -o yaml

Codificar uma string
	echo -n 'minha-senha' | tr -d \\n | base64 -w 0

Decodificar token na base 64
	echo <token> | base64 --decode

PARA REMOVER O KUBERNETES NO CENTOS 7

	kubeadm reset -y
	yum remove -y kubeadm kubectl kubelet kubernetes-cni kube*
	yum autoremove -y
	rm -rf ~/.kube
	rm -rf .kube
	rm -rf /etc/kubernetes/
		

===============

PODS===========

#VERSÃO PARA CRIAR UM POD
apiVersion: v1
#DEFININDO TIPO POD
kind: Pod
metadata:
  #NOME DO POD
  name: aplicacao
#ESPECIFICAÇÕES DO POD
spec:
  #DEFININDO OS CONTAINERS, PODE TER MAIS DE UM, MAS É RECOMENDADO TER APENAS UM CONTAINER POR POD
  containers:
    - name: container-aplicacao-loja
      image: rafanercessian/aplicacao-loja:v1
      ports:
        - containerPort: 80

DEPLOY=========

apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend-user
  labels:
    app: backend-user
spec:
  replicas: 2
  selector:
    matchLabels:
      app: backend-user
  template:
    metadata:
      labels:
        app: backend-user
    spec:
      containers:
      - name: backend-user
        image: fplima/backend-user:v1.1
        ports:
        - containerPort: 3020
        env:
        - name: NODE_ENV
          #DEFININDO O VALOR CONFIGURADO NO CONFIGMAP
          valueFrom:
            configMapKeyRef:
              #APONTANDO PARA O CONFIGMPAP DE NOME: questcode
              name: questcode
              #ASSOCIANDO O VALOR DA VARIÁVEL
              key: NODE_ENV
        - name: MONGO_URI
          #DEFININDO O VALOR CONFIGURADO NA SECRET
          valueFrom:
            secretKeyRef:
              #APONTANDO PARA A SECRET DE NOME: questcode
              name: questcode
              #ASSOCIANDO O VALOR DA VARIÁVEL
              key: MONGO_URI
        - name: SECRET_OR_KEY
          valueFrom:
            secretKeyRef:
              name: questcode
              key: SECRET_OR_KEY        

SERVICE=======

piVersion: v1
kind: Service
metadata:
  name: kubernetes-dashboard-nodeport-yaml
  #DEFININDO QUAL NAMESPACE SERÁ CRIADO
  namespace: kube-system
spec:
  #TIPO NODEPORT (EXPONDO O SERVIÇO)
  type: NodePort
  selector:
  	#QUAL O POD QUE ESTÁ SENDO APONTADO
    k8s-app: kubernetes-dashboard
  ports:
  - protocol: TCP
  	#PORTA PARA O SERVIÇO (NÃO É A PORTA QUE SERÁ EXPOSTA, ESTA SERÁ GERADA)
    port: 443
    #PORTA DE ACESSO AO CONTAINER DENTRO DO POD
    targetPort: 8443

NAMESPACES================

apiVersion: v1
kind: Namespace
metadata:
  name: staging
---
apiVersion: v1
kind: Namespace
metadata:
  name: prod
---
apiVersion: v1
kind: Namespace
metadata:
  name: devops
		
		
CONFIGMAP==========
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: questcode
  namespace: prod
data:
  NODE_ENV: production
  GITHUB_CLIENT_ID: 7c20984a5f9106c2fe22
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: questcode
  namespace: staging
data:
  NODE_ENV: staging
  GITHUB_CLIENT_ID: 7c20984a5f9106c2fe22


SECRET===========

 apiVersion: v1
kind: Secret
metadata:
  #NOME AO QUAL SERÁ REFERENCIADA PRA QUEM FOR USAR
  name: questcode
  #QUAL O NAMESPACE QUE IRÁ REFERENCIAR (kubectl get ns)
  namespace: prod
type: Opaque
#CHAVEAMENTO DAS VARIÁVEIS SECRETAS, SEUS VALORES DEVEM ESTAR EM BASE64 ()
data:
  MONGO_URI: bW9uZ29kYitzcnY6Ly90ZXN0ZTAyOnRlc3RlMDJAY2x1c3RlcjItYmVnMzIubW9uZ29kYi5uZXQvcXVlc3Rjb2RlP3JldHJ5V3JpdGVzPXRydWUmdz1tYWpvcml0eQ==
  SECRET_OR_KEY: ZHBhdWxhQGNoYXZlUFJPRA==
  GITHUB_CLIENT_SECRET: ZmY3NmE1YzAwY2RjMWI2ZmE0MWZlMTU2NjU1MmZlODMwM2ZmY2MyMg==
---
apiVersion: v1
kind: Secret
metadata:
  name: questcode
  namespace: staging
type: Opaque
data:
  MONGO_URI: bW9uZ29kYitzcnY6Ly90ZXN0ZTAyOnRlc3RlMDJAY2x1c3RlcjItYmVnMzIubW9uZ29kYi5uZXQvcXVlc3Rjb2Rlc3RhZ2luZz9yZXRyeVdyaXRlcz10cnVlJnc9bWFqb3JpdHk=
  SECRET_OR_KEY: ZHBhdWxhQGNoYXZlU1RBR0lORw==
  GITHUB_CLIENT_SECRET: ZmY3NmE1YzAwY2RjMWI2ZmE0MWZlMTU2NjU1MmZlODMwM2ZmY2MyMg==


SERVICE-ACOOUNT========

#ServiceAccount que será utilizada pelo tiller tiller-sa.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
#Arquivo allresources-clusterrole.yaml, com as configurações de permissões
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: allresources
rules:
- apiGroups: ["*"]
  resources: ["*"]
  verbs: ["*"]
---
#Associamos nossa ServiceAccount à ClusterRole através de um recruso chamado ClusterRoleBinding
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: tiller
subjects:
- kind: ServiceAccount
  namespace: kube-system
  name: tiller
  apiGroup: ""
roleRef:
  kind: ClusterRole
  name: allresources
  apiGroup: rbac.authorization.k8s.io
