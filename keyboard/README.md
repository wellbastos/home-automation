# Controle remoto bluetooth para seus dispositivos

## Teclado para automações no Home Assistant

[![Watch the video](https://img.youtube.com/vi/YEhD1XRVwC0/0.jpg)](https://www.youtube.com/watch?v=YEhD1XRVwC0)

## Pré-requisitos

1. Ter o teclado númerico bluetooth, o teclado usado no setupo é este: https://www.kalunga.com.br/prod/teclado-numerico-sem-fio-preto-kw50-app-tech-pilha-alcalina-palito-aaa-duracell-cx-1-un/996966


## Meu Setup

Meu home assitant é executado via docker compose num notebook I3 com o Ubuntu Linux.

# Identificando o Teclado no Linux:

Plugar o adaptador bluetooth na porta USB, e executar o comando `lsusb` no terminal.

Comando:
```shell
$ lsusb
```

Resultado esperado:
```shell
Bus 001 Device 097: ID 3151:3005 YICHIP Wireless Device
```

## Adicionando suporte ao teclado no Home assitant:

Adicionar o teclado no docker compose, como descrito no arquivo do `docker-compose.yaml`.

```yaml 
    volumes:
      - /dev/input:/dev/input
      - /dev/input/by-id/usb-YICHIP_Wireless_Device-event-kbd:/dev/input/by-id/usb-YICHIP_Wireless_Device-event-kbd
    device_cgroup_rules:
      - 'c 13:* rmw'    
    devices:
       - "/dev/input/"   
       - "/dev/input/by-id/usb-YICHIP_Wireless_Device-event-kbd"   
```

## Adicionando os eventos do teclado no Home Assistant:

Identificar os eventos com o comando `ls` no diretório `/dev/input`:

Comando:
```shell
$ ls /dev/input
```

Resultado esperado:
```shell
by-id    event0  event10  event12  event14  event16  event18  event2  event4  event6  event8  mice    mouse1
by-path  event1  event11  event13  event15  event17  event19  event3  event5  event7  event9  mouse0  mouse2
```

Estes eventos devem ser mapeados no arquivo `configuration.yaml` como no arquivo `configuration.yaml`.

## Identificar o id de cada tecla do teclado no Home Assistant:

Este teclado tem varios ids de tecla que podem ser utilizados, para identificar o id.
Acesse o Home Assistant, depois acesse `Ferramentas do Desenvolvedor`, depois em `eventos`, em `Ouvir Eventos` preencha com `keyboard_remote_command_received`, e clique em `ouvir`.

cada tecla pressionada irá retornar um id, esse id deve ser adicionado na sua automaçao!

### Exemplo:

A tecla `8` utilizo para acender a lampada do quarto, no meu teclado esta tecla tem o id `98`, logo ajusto o `key_code` com o valor `98`. 

como podemos ver na automação abaixo:

```yaml
- id: '1720877368650'
  alias: suite-liga-luz-rc
  description: ''
  trigger:
  - platform: event
    event_type: keyboard_remote_command_received
    event_data:
      device_descriptor: /dev/input/event4
      key_code: 98
      type: key_down
  condition: []
  action:
  - type: turn_on
    device_id: ed446a7160f6d69ccc11c6a786ae6b42
    entity_id: 6b9bf890b4efcc3b27757b52cdb451b7
    domain: switch
  mode: single
  ```

Agora é só aproveitar o brinquedo! rs

No Meu Setup tem estes dispositivos:

Suite:
1. Google Chromecast com TV.
2. Sound Bar LG SP8YA.
3. Broadlink RM4 Mini.
4. TV Sony Bravia 50.
5. Sonoff Mini flahseado com Tasmota.
6. Plafon Ekaza Dimerizavel Wifi.
7. AC LG dual inverter voice.

Quarto:
1. Google Chromecast com TV.
2. Broadlink RM4 Mini.
3. TV LG 32
4. Switch Zigbee.
5. Plafon Ekaza Dimerizavel Wifi.
6. AC LG dual inverter voice.

#homeassistant #hassio #home-assistant #home-automation
