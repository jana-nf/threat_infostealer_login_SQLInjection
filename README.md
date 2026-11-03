# 🛡️ Threat Correlator: Infostealer → Login Legítimo → SQL Injection

Motor de correlação de eventos de segurança (estilo mini-SIEM) desenvolvido em Python para detecção avançada de ameaças. 

O projeto correlaciona eventos isolados de baixa severidade que, quando combinados, formam uma **cadeia de ataque (kill chain)** crítica, disparando alertas automatizados em tempo real via Telegram.

---

## ⚠️ O Problema

Em ambientes corporativos modernos, as equipes de segurança sofrem com uma enxurrada de falsos positivos e alertas desconectados:

1. **Vazamentos de Credenciais:** Contas corporativas vazadas em fóruns clandestinos ou logs de *infostealers* (ex.: RedLine, Lumma) costumam gerar apenas um aviso genérico de médio risco.

2. **Logins Anômalos:** Acessos bem-sucedidos de IPs ou países desconhecidos muitas vezes passam despercebidos se analisados de forma isolada[cite: 2].

3. **Varreduras e Ataques Web:** Tentativas de *SQL Injection* ou varreduras de WAF ocorrem constantemente e podem ser tratadas apenas como bloqueios pontuais de firewall.

**O desafio:** Identificar quando um atacante utiliza uma credencial roubada de um funcionário para realizar um login legítimo e, em seguida, executa um ataque de injeção de código na aplicação web utilizando a mesma sessão.

---

## 💡 A Solução

O **Threat Correlator** atua como um motor determinístico baseado em estados que unifica diferentes fontes de telemetria para mitigar esse cenário:

* **Correlação de Kill Chain em 3 Estágios:**

  * **Estágio 1:** Detecção de vazamento de credencial corporativa via feeds de *Threat Intelligence*[cite: 2].

  * **Estágio 2:** Monitoramento de login bem-sucedido com a conta comprometida a partir de um IP ou país anômalo[cite: 2].

  * **Estágio 3:** Identificação de *SQL Injection* (com suporte a detecção de assinaturas e dupla codificação URL) vindo exatamente da mesma sessão/IP do login suspeito[cite: 2].

* **Escalonamento Dinâmico de Severidade:** A criticidade do alerta aumenta automaticamente conforme mais estágios da cadeia são confirmados (🟡 Médio ➔ 🟠 Alto ➔ 🔴 Crítico, incluindo detecção de possível exfiltração de dados por volume de bytes)[cite: 2].

* **Arquitetura de Produção:**

  * **GeoIP Real:** Enriquecimento de geolocalização utilizando a base MaxMind GeoLite2[cite: 2].

  * **Persistência com SQLite:** Armazenamento seguro de baselines de IPs conhecidos e sessões ativas para sobreviver a reinicializações do sistema[cite: 2].

  * **Correlação Retroativa:** Capacidade de associar um login anômalo prévio caso o relatório de vazamento de credencial chegue atrasado[cite: 2].

  * **Proteção contra Brute-force:** Contagem de falhas para detectar ataques de *Credential Stuffing*[cite: 2].

  * **Fila Assíncrona (Worker):** Envio em alto volume para o Telegram de forma assíncrona e thread-safe, respeitando limites de taxa (*rate limits*)[cite: 2].

---

## 🛠️ Tecnologias Utilizadas

* **Python 3.10+** (Tipagem estrita com `dataclasses` e `Enum`)

* **SQLite** (Persistência leve de estado)

* **GeoIP2 / MaxMind** (Enriquecimento de geolocalização)

* **Telegram Bot API** (Notificações em tempo real com tratamento de falhas e *retries*)

* **Unittest** (Testes automatizados cobrindo cenários de correlação e resiliência)

---

## 🚀 Como Executar

1. Clone o repositório:
   ```bash
   git clone [https://github.com/seu-usuario/threat-correlator.git](https://github.com/seu-usuario/threat-correlator.git)
   cd threat-correlator
   ```

