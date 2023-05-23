# TechLogistics

### Apresentação
Olá, bem vindo(a) ao projeto da empresa TechLogistics. Neste projeto, solicitei a IA ChatGPT para que gerasse um caso de uso acerca de uma empresa que possuí sua infraestrutura hospedada no on-presmise e deseja migrar para nuvem.

## 📃 Caso de uso
A TechLogistics atua no setor de logística e transporte e deseja migrar sua infraestrutura local. No entanto, a equipe de TI da TechLogistics percebeu que a infraestrutura on-premise está ficando cara de manter e limita sua capacidade de escalar rapidamente para atender à demanda crescente dos clientes. Além disso, eles estão preocupados com a segurança, a disponibilidade e a confiabilidade de seus sistemas.

Diante desses desafios, a liderança da TechLogistics decidiu migrar sua infraestrutura para a nuvem, especificamente para a AWS. Eles acreditam que a AWS oferece a escalabilidade, a flexibilidade e a confiabilidade necessárias para atender às demandas em constante mudança do mercado.

Aqui estão os principais pontos da migração da TechLogistics para a AWS:

- **Armazenamento de dados:** A empresa precisa armazenar uma grande quantidade de dados relacionados aos pedidos de transporte, informações de rastreamento e detalhes dos clientes. Além disso, é necessário que esses dados estejam altamente disponíveis e sejam escaláveis.

- **Processamento de cargas de trabalho:** A TechLogistics tem picos de demanda em determinados momentos do ano, como durante as festas de fim de ano. É essencial que a infraestrutura na nuvem possa lidar com esses picos de carga e dimensionar automaticamente recursos adicionais quando necessário.

- **Segurança dos dados:** A empresa lida com informações confidenciais dos clientes, como endereços, números de telefone e detalhes de pagamento. A TechLogistics precisa garantir a segurança desses dados durante a migração e após ela.

- **Banco de dados:** A empresa possui um aplicativo interno que armazena informações sobre rotas, horários e veículos. É necessário um serviço de banco de dados escalável para hospedar esses dados.

- **Processamento de eventos em tempo real:** A TechLogistics está interessada em obter insights em tempo real sobre os eventos de transporte, como a localização dos veículos e as atualizações de entrega. É importante que a infraestrutura possa processar e analisar esses eventos em tempo real.

## 💡 Diagrama da Implantação
![](https://github.com/vitor-antoni/aws-building-architectures/blob/main/TechLogistics/TechLogistics%20Diagrama.svg "Diagrama de implantação")

### Observações sobre IP's
- *Private Route Table*
  | Destino        | Alvo/Target          |
  |:--------------:|:--------------------:|
  | 192.168.1.0/24 | local                |
  | 0.0.0.0/0      | nat-gateway-endpoint |
  
- *Públic Route Table*
  | Destino        | Alvo/Target          |
  |:--------------:|:--------------------:|
  | 192.168.1.0/24 | local                |
  | 0.0.0.0/0      | igw-gateway-endpoint |

## 💷 Custos de implantação
> Foi realizada uma *estimativa* de custos utilizando o serviço Princing Calculator da AWS.

Para verificar a estimativa de custos, entre no site do AWS Pricing Calculator [clicando aqui](https://calculator.aws/#/estimate?id=a05a39a82aec6f8da78674d832d534578b616b97) ou confira a tabela abaixo.

|  ***Serviço***  | ***Custos p/ mês*** | ***Custos Anual*** |
|:---------------:|--------------------:|-------------------:|
|Route53          |$ 2,21               |$ 26,52             |
|CloudFront       |$ 133,50             |$ 1.602,00          |
|WAF              |$ 10,00              |$ 120,00            |
|LoadBalancer     |$ 308,43             |$ 3.701,16          |
|EC2              |$ 560,32             |$ 6.723,84          |
|VPC              |$ 65,74              |$ 788,88            |
|RDS              |$ 575,62             |$ 6.907,44          |
|CloudWatch       |$ 8,51               |$ 102,12            |
|CodeDeploy       |$ 3,60               |$ 43,20             |
|CodePipeline     |$ 2,00               |$ 24,00             |
|S3               |$ 0,01               |$ 0,12              |
|GuardDuty        |$ 6,06               |$ 72,72             |
|***Custo Total***|$ 2384,64            |$ 20.111,05         |

## 💼 Descritivo técnico dos serviços selecionados
Nesta sessão, comentarei um pouco acerca dos serviços selecionados e uma breve instrução de como será configurado.

- ***Route53:*** Este é o serviço de DNS da AWS, logo, com ele será possível utilizar um domínio próprio da empresa TechLogistics para acessar os recursos da VPC a partir da Internet utilizando um domínio.
- ***CloudFront:*** Este serviço vai ser responsável por entregar conteúdo com baixa latência aos usuários, dado que uma vez que um dado é requisitado, ele é armazenado em cache para que a próxima requisição seja mais rápida.
- ***WAF***: Este serviço servirá de Firewall de LoadBalancer, para impedir que ataques de SQL Injection sejam realizados.
- ***LoadBalancer***: O balanceador de carga trabalhará dividindo e direcionado o tráfego entre as instâncias EC2 do Auto Scaling.
- ***EC2***: Para recursos de computação, foi selecionada o tipo de instância otimizada para computação, logo, uma das escolhas mais adequadas e de custo benefício é a `c6g.xlarge`, com 4vCPU e 8GiB de memória. Serão 4 instâncias operantes em duas diferentes zonas de disponibilidade mas que poderá escalar, sob-demanda, até 6 instâncias.
- ***VPC***: O custo de VPC é referente ao NAT Gateway.
- ***RDS***: Serviço de banco de dados com implantação Multi-AZ (com uma instância em standby em AZ diferente da instância RDS de origem) e uma Read replica (réplica de leitura) para a aplicação realizar apenas consultas.
- ***CloudWatch***: Monitoramento gráfico e de logs da infraestrutura.
- ***CodeDeploy***: Para realizar atualizações na aplicação, atualizando diretamente as instâncias já operantes e as instâncias futuras que serão iniciadas pelo AutoScaling.
- ***CodePipeline***: Este serviço, após configurado, poderá monitorar as versões de um determinado arquivo no S3 Bucket para que sempre que for atualizado, realizar uma implantação no CodeDeploy a partir deste arquivo.
- ***S3 Bucket***: Será criado um Bucket do S3 para que os desenvolvedores possam realizar o upload do arquivo da aplicação atualizado, sendo aplicada atualização através do CodePipeline e CodeDeploy.
- ***GuardDuty***: Este serviço é importante para nós monitoramos as vulnerabilidades em nossa infraestrutura.

