# Configuração do decodificador de log Zimbra para agente Wazuh

Este guia descreve as etapas para configurar o agente Wazuh para coletar e decodificar logs do Zimbra.

## Etapas de configuração

### Etapa 1: Configurar a coleta de logs do Zimbra

1. Edite o arquivo de configuração do agente Wazuh localizado em `/var/ossec/etc/ossec.conf`.

2. Adicione o seguinte snippet de configuração XML para especificar o local do arquivo de log do Zimbra:

```xml
     <arquivo local>
       <log_format>syslog</log_format>
       <location>/opt/zimbra/log/audit.log</location>
     </localfile>
```
### Etapa 2: reinicie o agente Wazuh

Reinicie o agente para aplicar as alterações:

```bash
systemctl reiniciar agente wazuh
```

### Etapa 3: sobreescrever regra 2501

Por padrão, os logs do Zimbra acionarão a regra 2501. Para evitar conflitos com regras personalizadas do Wazuh, você precisa substituir esta regra.

Edite o arquivo de regras personalizadas em seu servidor Wazuh.

Adicione a seguinte regra, alterando o nível conforme necessário (os valores recomendados são 0 ou 3):

```xml
<rule id="2501" level="0" overwrite="yes">
  <match>FAILED LOGIN |authentication failure|</match>
  <match>Authentication failed for|invalid password for|</match>
  <match>LOGIN FAILURE|auth failure: |authentication error|</match>
  <match>authinternal failed|Failed to authorize|</match>
  <match>Wrong password given for|login failed|Auth: Login incorrect|</match>
  <match>Failed to authenticate user</match>
  <group>authentication_failed,pci_dss_10.2.4,pci_dss_10.2.5,gpg13_7.8,gdpr_IV_35.7.d,gdpr_IV_32.2,hipaa_164.312.b,nist_800_53_AU.14,nist_800_53_AC.7,tsc_CC6.1,tsc_CC6.8,tsc_CC7.2,tsc_CC7.3,</group>
  <description>syslog: User authentication failure.</description>
</rule>
```

### Etapa 4: adicionar regras Wazuh personalizadas
Adicione suas regras Wazuh personalizadas, garantindo que elas tenham um nível superior à regra 2501 para priorizá-las adequadamente.

### Conclusão
Depois de concluir essas etapas, seu agente Wazuh estará configurado para coletar e analisar efetivamente os logs do Zimbra.
