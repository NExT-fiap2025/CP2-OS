# Guia Rápido de Instalação e Teste dos Serviços de Monitoramento

Este guia fornece as instruções para instalar, configurar e verificar os dois scripts de monitoramento (`auth_monitor` e `who_monitor`) em um sistema Linux com `systemd`.

## Passo 1: Preparação dos Arquivos

Antes de começar, certifique-se de que você tem os quatro arquivos do projeto (`auth_monitor.sh`, `auth_monitor.service`, `who_monitor.sh`, `who_monitor.service`) em um diretório no seu computador.

Se você recebeu os arquivos em um pacote `checkpoint_entrega.tar.gz`, primeiro descompacte-o:
```bash
# Descompacta o arquivo e entra na pasta criada
tar -xzvf checkpoint_entrega.tar.gz
cd checkpoint_entrega
```

## Passo 2: Instalação

Com os arquivos prontos, execute os seguintes comandos no terminal para movê-los para os diretórios corretos do sistema e aplicar as permissões necessárias.

**1. Mover os scripts executáveis:**
```bash
sudo mv auth_monitor.sh who_monitor.sh /usr/local/bin/
```

**2. Mover os arquivos de serviço do systemd:**
```bash
sudo mv auth_monitor.service who_monitor.service /etc/systemd/system/
```

**3. Dar permissão de execução aos scripts:**
```bash
sudo chmod +x /usr/local/bin/auth_monitor.sh /usr/local/bin/who_monitor.sh
```

**4. Recarregar o systemd para reconhecer os novos serviços:**
```bash
sudo systemctl daemon-reload
```

## Passo 3: Execução e Verificação

Agora que tudo está instalado, vamos iniciar os serviços e confirmar que estão funcionando corretamente.

**1. Iniciar os dois serviços:**
```bash
sudo systemctl start auth_monitor.service who_monitor.service
```

**2. Verificar o status:**
Execute o comando abaixo. Você deve ver o status **`active (running)`** em verde para ambos os serviços.
```bash
sudo systemctl status auth_monitor.service who_monitor.service
```
> Pressione `q` para sair da tela de status.

**3. Realizar os Testes de Funcionamento:**

#### Teste do Monitor de Usuários (`who_monitor`)
1.  Aguarde cerca de 30 segundos.
2.  Verifique o arquivo de log:
    ```bash
    cat /var/log/who_monitor_log.log
    ```
3.  **Resultado Esperado:** O terminal deve exibir a lista de usuários logados no momento, precedida por um timestamp.

#### Teste do Monitor de Autenticação (`auth_monitor`)
Este teste requer o uso de dois terminais.

1.  **No Terminal 1**, execute o comando abaixo para "escutar" o arquivo de log.
    > **Nota:** É normal que este comando retorne "No such file or directory" na primeira vez, antes que qualquer atividade seja registrada.
    ```bash
    tail -f /var/log/auth_monitor_log.log
    ```
2.  **No Terminal 2**, execute um comando que exija privilégios de administrador, como:
    ```bash
    sudo ls -l
    ```
3.  **Resultado Esperado:** Volte para o **Terminal 1**. Uma nova linha de log, com data e hora, detalhando a sua atividade com `sudo`, deve ter aparecido **instantaneamente**.

---

Se todos os testes foram bem-sucedidos, os serviços estão instalados e operando corretamente no seu sistema.
