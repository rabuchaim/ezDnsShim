# ezDnsShim v1.0.0
ezDnsShim é uma biblioteca em Python3 para trabalhar com o servidor de DNS desenvolvido e utilizado pelo Registro.br para gerenciar os domínios da internet brasileira. Retorna a resposta em JSON ao invés de XML, tudo consolidado em um único arquivo python com códígo fonte comentado e sem dependências de outras bibliotecas, pure python! [For an English version, click here](https://github.com/rabuchaim/ezDnsShim/README-English.md)

O DnsShim é um servidor de DNS de código aberto, desenvolvido em Java pelo Registro.br e pode ser utillizado por qualquer empresa ou provedor de serviços de internet. Consiste em um servidor de DNS autoritativo MASTER que fica isolado, sem possibilidade de acesso externo. A gerência das zonas de DNS são feitas somente nesse servidor DnsShim, ele faz as publicações nos servidores de DNS autoritativos secundários que ficam disponíveis ao público e respondendo com autoridade sobre as zona de DNS ali cadastradas. Esses servidores autoritativos secundários utilizam o bind e ficam recebendo as atualizações do MASTER sempre que houver uma nova publicação. 

O protocolo de comunicação com esse servidor é baseado em XML. A bibliteca ezDnsShim retorna uma variável do tipo dict (como um json) que facilita muito a gerência desse serviço. Há tratamentos de exceptions com mensagens de erro claras e disponíveis em Português e Inglês.

O código foi baseado na biblioteca ```pydnsshim v1.5```, também desenvolvida pelo Registro.br e disponível para download em https://www.registro.br/dnsshim

## How Easy?

```python
from ezdnsshim import ezDnsShimClient

user_to_add = 'dnsadmin'

conn = ezDnsShimClient(server='127.0.0.1',port=9999)
response = conn.addUser(username=user_to_add,password='*********')
if response['status'] == responseCode.OK:
    print(f"Username {user_to_add} created with success!")
else:
    print(f"Failed to add the username {user_to_add} - Reason: {response['msg']} - Status Code: {response['status']}")
    sys.exit(1)
    
with ezDnsShimClient(server='127.0.0.1',port=9999,username='dnsadmin',password='*********',debug=False,keep_alive=True) as conn:
    if conn.isAuthenticated: 
        print(f"LOGGED IN! SessionID {conn.sessionId}")
        zone_to_add = 'mynewdnszone1.com.br'
        if not conn.zoneExists(zone=zone_to_add):
            response = conn.newZone(zone=zone_to_add,slaveGroup='slavegroup-sa-east')
            if response['status'] == responseCode.OK:
                print(f"Your new zone '{zone_to_add}' was created with success! [{response['elapsedTime]}'s]")
                print(json.dumps(response,indent=4,sort_keys=False))
        else:
            print(f"The zone {zone_to_add} already exists on this server!")
```
Output:
```bash
LOGGED IN! SessionID 1527298117
Your new zone 'mynewdnszone1.com.br' was created with success! [0.18062's]
{
    "status": 1,
    "newZone": {
        "zone": "mynewdnszone1.com.br",
        "slaves": {
            "slave": [
                {
                    "slaveAddress": "10.10.10.61",
                    "slavePort": "53"
                },
                {
                    "slaveAddress": "10.10.10.71",
                    "slavePort": "53"
                }
            ]
        },
        "key": "20240122002100-ZSK-257-61062",
        "keytag": "61062",
        "digestType": "SHA256",
        "digest": "2f90743ef64d8f30544b58f1ae124cab1bf95660304897bb52b0e01e97a658f1"
    },
    "elapsedTime": 0.18062
}
```
## Fase final de testes

Está sendo realizado os últimos testes e estamos implementado algumas validações e blindagens contra erros de input do usuário. A biblioteca estará disponivel até o final de Jan/2024.

