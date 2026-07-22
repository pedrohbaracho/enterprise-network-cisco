# **Documentação de Arquitetura e Infraestrutura de Rede Empresarial**

**Projeto:** Implementação, Mapeamento, Monitoramento e Segurança de Rede Corporativa  
**Autor / Responsável:** Pedro Henrique Baracho de Oliveira

## **1\. Escopo e Objetivos do Projeto**

---

Esta documentação especifica o projeto e a implementação da infraestrutura de rede corporativa desenvolvida no Cisco Packet Tracer. A arquitetura foi desenhada para garantir **alta disponibilidade, redundância, segurança e gerenciamento centralizado**, atendendo aos padrões corporativos através das seguintes implementações:

* **Segmentação e Roteamento:** Configuração de VLANs corporativas e Roteamento Inter-VLAN.  
* **Disponibilidade e Redundância:** Aplicação de EtherChannel (agregação de enlaces), HSRP (redundância de gateway) e STP/PVST+ (prevenção de loops de Camada 2).  
* **Segurança e Hardening:** Aplicação de Access Control Lists (ACLs) para isolamento de tráfego sensível e criptografia corporativa de credenciais.  
* **Gerenciamento e Monitoramento:** Sincronização temporal via NTP autenticado, centralização de logs via Syslog e descoberta de topologia via protocolo LLDP.

## **2\. Especificações e Configurações Técnicas**

### ---

**2.1 Tabela de Endereçamento IP e Segmentação (VLSM)**

A rede foi segmentada em VLANs para isolamento de broadcast e otimização de endereçamento via VLSM:

| VLAN | Setor / Função | Sub-rede | Máscara de Rede | Gateway Padrão   |
| :---- | :---- | :---- | :---- | :---- |
| **10** | Administração (ADM) | 192.168.0.0/28 | 255.255.255.240 | 192.168.0.1 |
| **20** | Vendas | 192.168.1.0/27 | 255.255.255.224 | 192.168.1.1 |
| **30** | Tecnologia da Informação (TI) | 192.168.2.32/27 | 255.255.255.224 | 192.168.2.33 |
| **40** | Servidores Corporativos | 192.168.3.40/29 | 255.255.255.248 | 192.168.3.41 |
| **99** | Gerenciamento de Ativos | 10.0.0.0/24 | 255.255.255.0 | 10.0.0.1 |

### **2.2 Serviços de Rede (DHCP)**

* Pools de DHCP configurados e centralizados nos Switches Multilayer (Layer 3).  
* Atribuição dinâmica de IP, máscara e gateways para os hosts de cada VLAN.

### **2.3 Camada de Comutação (Switches)**

* **Switches de Acesso (SW-ADM, SW-VENDAS, SW-TI, SW-SERVER):**  
  * Portas de acesso associadas estritamente às suas respetivas VLANs.  
  * Portas de interconexão configuradas em modo Trunk.  
* **Switches Multilayer / Core (SWL3-1 e SWL3-2):**  
  * Habilitado roteamento inter-VLAN (ip routing).  
  * Enlaces agregados via EtherChannel (LACP) para alta largura de banda e redundância física.  
  * Atuação no protocolo HSRP para redundância de gateway na camada de distribuição.  
  * Implementação de Spanning Tree Protocol (PVST+) com atribuição estratégica de prioridades de Root Bridge por VLAN.

### **2.4 Camada de Roteamento e Borda**

* **R1\_ACTIVE (Cisco ISR4331):** Roteador principal de saída para a Internet/Borda.  
* **R1\_STANDBY (Cisco ISR4331):** Roteador de backup redundante via HSRP.  
* Configuração de rotas estáticas em ambos os roteadores cobrindo todas as sub-redes internas.

### **2.5 Servidores e Gerenciamento Centralizado**

* **Servidor Syslog (192.168.3.43):** Centralização de registros e logs de eventos de todos os ativos de rede (logging host).  
* **Servidor NTP (192.168.3.43):** Sincronização temporal de relógios dos dispositivos utilizando autenticação segura (Chave: 1010).

### **2.6 Segurança e Hardening**

* **Políticas de ACL (Access Control List):** Aplicada na VLAN 10 (ADM) para isolamento do tráfego administrativo.  
* **Restrição de Serviços:** Acesso da VLAN ADM para a zona de servidores limitado estritamente aos serviços de NTP e Syslog.  
* Criptografia global de senhas e credenciais habilitada em todos os ativos da infraestrutura.

## **3\. Topologia Lógica da Rede**

* ---

  **Protocolos de Mapeamento e Diagnóstico:** LLDP habilitado globalmente para identificação e documentação automática de vizinhança e conexões físicas/lógicas.

## **4\. Considerações de Arquitetura e Resiliência**

1. ---

   **Redundância de Borda:** O protocolo HSRP assegura a continuidade de negócios e o chaveamento automático de tráfego caso o roteador principal falhe.  
2. **Estabilidade L2:** A combinação de EtherChannel e PVST+ previne broadcast storms e loops mantendo caminhos redundantes ativos.  
3. **Auditoria e Governança:** O servidor Syslog e a sincronização do NTP garantem logs precisos para análise de segurança e conciliação de incidentes.

## **5\. Conclusão**

---

A solução desenvolvida entrega uma infraestrutura robusta, escalável e resiliente, pronta para sustentar operações corporativas críticas com o mais alto nível de disponibilidade e controle de segurança.