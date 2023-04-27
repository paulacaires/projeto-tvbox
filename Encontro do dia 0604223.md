# Encontro dia 06/04/2023

Placa TX9
Versão do Armbian: 6.2.9 lunar edge cli

No encontro do dia 06/04/2023, fizemos passo a passo da instalação do Armbian em uma placa TX9, na tentativa de reconhecer a placa de Wi-Fi, utilizando uma técnica que o Mário descobriu.

O objetivo é manipular a máquina pela rede, sem precisar do cabo HDMI.

- Instalar o [Balena Etcher](https://www.balena.io/etcher), pegar o Multitool do fórum e colocar no cartão SD.
- Versão do Armbian instalada: 6.2.9 lunar edge cli. A imagem compactada é xz.
- Colocar essa imagem para a pasta `images` do Multitool.
- Bootar na máquina.
- Com o cartão na máquina, ligar ela.
- Pra usar o Multitool com o SSH, precisa rodar o comando `multitool.sh` após a maquina ligar.

```bash
nmap -sn <Seu ip>
```

Esse comando pinga todos os IPs do host que se deseja escanear. O host nesse caso é o seu próprio computador. O objetivo é descobrir o IP da TV Box.

```bash
ssh root@<IP da TV Box>
```

Conectamos direto o cabo de rede no notebook. Tem que ver qual o IP que a TV Box pegou quando reinicia, conecta pela rede sem necessidade de HDMI.

- Quanto terminar de instalar o Armbian, faz o reboot.
- Tira o cartão e liga na tomada novamente a TV Box.
- É importante tirar o cartão somente quando cortar a energia (Ou seja, o cabo cuja entrada é “redonda”).
- Não tirar o cartão SD com a TV Box com energia ligada, pode dar problema de leitura e no cartão.
- Quando rebootar, pode dar um problema no IP da Tv Box. Tem que procurar o IP novamente, conforme descrito anteriormente, mas pode dar problema de chave porque mudou a máquina, mas é o mesmo IP, então dá erro.
- A solução é apagar a chave SSH com o comando que a própria mensagem de erro deu.

Para instalar o Armbian:

- Senha: 1234
- Quando for escolher as opções, **digitar só o número** sem dar enter.
- user: user
- senha: user 1234
- Cuidado com os enters!

Escolher as opções

- (124) PT_BR UTF-8
- (2) Americas
- (9) Brazil
- (8) São Paulo

Assim, o Armbian está instalado. 

---

No Armbian.

```bash
ls /sys/bus/sdio/devices
```

Breve explicação:

- `sys` é do Linux;
- `bus` são os barramentos;
- `sdio` é um barramento específico;
- `devices` lista todos os dispositivos identificados, onde identifica a conexão com o Wi-Fi.

Se identificar a placa de Wi-Fi, ele retorna algo desse comando. 

Outros comandos úteis:

```bash
lsmod
```

Mostra o módulo da placa de rede se encontrar.

```bash
dmesg | grep ssv
```

Mostra o log das coisas.

```bash
dmesg | grep mmc0
dmesg | grep mmc1
dmesg | grep mmc2
```

Rodar um de cada vez com o objetivo de tentar encontrar o Wi-Fi.

```bash
cd /boot/
cat armbianEnv.txt
```

Informações de configuração do hardware padrão da Tv Box, dizendo o que tem de carregar durante o boot. É uma device tree. 

```bash
cd dtb
```

Tem outras arquiteturas que veio juntos com o Armbian para carregar.

Usar as devices trees padrões e mais duas adicionais.

- Ir do cinco para a quatro sem mudar as portas.
    - Comando: `rk322x-config`
    - Dar enter em todas as opções padrão.
    - E selecionar, quando aparecer, o led 5.
    - Fazer o reboot.
    - Fazer o `ssh root` tudo novamente, inserir a senha etc.
- Logar novamente.
- Bootar com o led cinco e não reconhece a Internet.
- Faz o mesmo procedimento para o led quatro e rebota sem tirar a energia, ou seja, faz mudanças em cima do led cinco.
- Dessa forma, a placa de rede vai ser identificada, mas quando desligar a máquina essa configuração será perdida.