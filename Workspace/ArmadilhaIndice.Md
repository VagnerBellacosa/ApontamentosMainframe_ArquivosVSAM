# Armadilha de índice

Última atualização : 29/01/2025

Para VSAM RLS, há uma armadilha de índice que verifica cada registro de índice antes de gravá-lo. A armadilha detecta as seguintes corrupções de índice:

- Alta utilização maior que alta alocação
- Ponteiro de índice duplicado ou inválido
- Registro de índice fora de sequência
- Entrada de seção inválida
- Comprimento de chave inválido.

Para obter mais informações sobre a armadilha de índice VSAM RLS para programadores de sistema, consulte [Diagnóstico DFSMSdfp do z/OS](https://www.ibm.com/docs/en/SSLTBW_3.1.0/com.ibm.zos.v3r1.idar100/abstract.htm) .

**Tópico pai:**

[Usando o compartilhamento em nível de registro VSAM](https://www.ibm.com/docs/en/SSLTBW_3.1.0/com.ibm.zos.v3r1.idad400/rls.htm)