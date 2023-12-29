# promtail-no-linux

Criando serviço do Promtail no Linux

Baixar o binário do promtail:
curl -O -L "https://github.com/grafana/loki/releases/download/v2.4.1/promtail-linux-amd64.zip"
Extrair binário
unzip "promtail-linux-amd64.zip"
Tornar executavel
chmod a+x "promtail-linux-amd64"
copiar binário para /usr/local/bin/
sudo cp promtail-linux-amd64 /usr/local/bin/promtail
Verificar instalação/versão
promtail --version

Criando o config do promtail
Create dir in /etc
mkdir -p /etc/promtail /etc/promtail/logs

Criando arquivo: (cole o bloco abaixo e realize ajustes conforme necessário)

vim /etc/promtail/promtail-config.yaml

server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /etc/promtail/positions.yaml

clients:
  - url: http://localhost:3100/loki/api/v1/push #atualizar com a url do loki

scrape_configs: #informações que serão coletadas
- job_name: nginx
  static_configs:
  - targets:
      - localhost
    labels:
      job: nginx # nome que aparecerá no grafana
      hostname: your-hostname # nome que aparecerá no grafana
      __path__: /var/log/nginx/*.log # caminho do log

Criando o serviço do promtail

vim /etc/systemd/system/promtail.service

Adicione as informações

[Unit] 
Description=Promtail service 
After=network.target 
 
[Service] 
Type=simple 
User=root 
ExecStart=/usr/local/bin/promtail -config.file /etc/promtail/promtail-config.yaml 
Restart=on-failure 
RestartSec=20 
StandardOutput=append:/etc/promtail/logs/promtail.log 
StandardError=append:/etc/promtail/logs/promtail.log 
 
[Install] 
WantedBy=multi-user.target


Execute o promtail

sudo systemctl daemon-reload #To reload systemd
sudo systemctl start promtail #to start promtail
sudo systemctl status promtail #to check status
sudo systemctl restart promtail #to restart

Habilitar na inicialização 

systemctl enable promtail.service



ref. https://psujit775.medium.com/how-to-setup-promtail-in-ubuntu-20-04-ed652b7c47c3
