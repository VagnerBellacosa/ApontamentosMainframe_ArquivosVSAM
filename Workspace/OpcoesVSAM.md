# Opções VSAM

Última atualização : 29/01/2025

Usando o VSAM, você pode obter blocos de controle, buffers e vários pools de recursos compartilhados locais (LSR) com mais ou menos de 16 MB. No entanto, se o seu programa usar um endereço de 24 bits, ele poderá gerar uma verificação de programa se você tentar referenciar blocos de controle, buffers ou pools de LSR com mais de 16 MB. Com um endereço de 24 bits, você não terá capacidade de endereçamento para os buffers de dados.

Se você especificar que blocos de controle, buffers ou pools podem estar acima da linha e tentar usar o modo de localização para acessar registros enquanto estiver no modo de 24 bits, seu programa executará uma verificação de programação (ABEND 0C4).



**Restrição:** Não é possível especificar a localização de buffers ou blocos de controle para processamento RLS. O RLS ignora a palavra-chave ACB RMODE31= .

Ao usar endereços de 31 bits, observe as seguintes regras:

- Todos os blocos de controle VSAM que contêm endereços devem conter endereços válidos de 31 bits. Se estiver usando endereços de 24 ou 31 bits, não use o byte de ordem superior de um campo de endereço de 31 bits como um campo de sinalizador definido pelo usuário.
- Buffers de E/S e blocos de controle podem ser obtidos acima ou abaixo de 16 MB de armazenamento.
  - Buffers de E/S e blocos de controle podem ser solicitados abaixo de 16 MB usando os padrões de macro ACB, GENCB, MODCB ou BLDVRP.
  - Buffers de E/S podem ser solicitados acima de 16 MB e blocos de controle abaixo de 16 MB especificando o parâmetro RMODE31=BUFF nas macros ACB, GENCB, MODCB ou BLDVRP.
  - Blocos de controle podem ser solicitados acima de 16 MB e buffers abaixo de 16 MB especificando o parâmetro RMODE31=CB nas macros ACB, GENCB, MODCB ou BLDVRP.
  - Blocos de controle e buffers podem ser solicitados acima de 16 MB especificando o parâmetro RMODE31=ALL nas macros ACB, GENCB, MODCB ou BLDVRP.
  - Os buffers são obtidos no armazenamento endereçável de 24 bits especificando o subparâmetro RMODE31=NONE ou CB no parâmetro AMP.
  - Os blocos de controle são obtidos no armazenamento endereçável de 24 bits especificando o subparâmetro RMODE31=NONE ou BUFF no parâmetro AMP.
- A lista de parâmetros passada para a rotina de saída do UPAD e do JRNAD reside na mesma área especificada com os blocos de controle VSAM. Se RMODE31=CB ou RMODE31=ALL for especificado, a lista de parâmetros reside acima de 16 MB.
- Você deve recompilar a parte do seu programa que contém as especificações de macro ACB, BLDVRP e DLVRP.
- Você especifica parâmetros de 31 bits especificando o parâmetro AMP=(RMODE31=) no JCL.

[A Tabela 1](https://www.ibm.com/docs/en/zos/3.1.0?topic=vsam-options#vsamo__kps) resume os parâmetros de palavras-chave de endereço de 31 bits e seu uso nas macros VSAM aplicáveis.

| MACRO  | RMODE31=                                                     | MODO=                                                        | LOC=                                                         |
| ------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ACB    | Local de armazenamento virtual de blocos de controle VSAM e buffers de E/S | INVÁLIDO                                                     | INVÁLIDO                                                     |
| BLDVRP | Local de armazenamento virtual do pool VSAM LSR, blocos de controle VSAM e buffers de E/S | Formato da lista de parâmetros BLDVRP (formato de 24 bits ou 31 bits) | INVÁLIDO                                                     |
| FECHAR | INVÁLIDO                                                     | Formato da lista de parâmetros CLOSE (formato de 24 bits ou 31 bits) | INVÁLIDO                                                     |
| DLVRP  | INVÁLIDO                                                     | Formato da lista de parâmetros DLVRP (formato de 24 ou 31 bits) | INVÁLIDO                                                     |
| GENCB  | Valores RMODE31 a serem colocados no ACB que está sendo criado. Quando o ACB gerado for aberto, os valores RMODE31 determinarão o local de armazenamento virtual dos blocos de controle VSAM e dos buffers de E/S. | INVÁLIDO                                                     | Local para o armazenamento virtual obtido pelo VSAM para ACB, RPL ou EXIT LIST. |
| MODCB  | Valores RMODE31 a serem colocados em um ACB especificado     | INVÁLIDO                                                     | INVÁLIDO                                                     |
| ABRIR  | INVÁLIDO                                                     | Formato da lista de parâmetros OPEN (formato de 24 bits ou 31 bits) | INVÁLIDO                                                     |

Leitura relacionada:

- Consulte [Obtendo buffers acima de 16 MB](https://www.ibm.com/docs/en/SSLTBW_3.1.0/com.ibm.zos.v3r1.idad400/buf31.htm#buf31) para obter informações sobre como criar e acessar buffers que residem acima de 16 MB.
- Consulte [Compartilhamento de recursos entre conjuntos de dados VSAM](https://www.ibm.com/docs/en/SSLTBW_3.1.0/com.ibm.zos.v3r1.idad400/lsr.htm#lsr) para obter informações sobre como criar vários pools LSR em um espaço de endereço.
- Consulte [a Referência JCL do z/OS MVS](https://www.ibm.com/docs/en/SSLTBW_3.1.0/com.ibm.zos.v3r1.ieab600/abstract.htm) para obter informações sobre como especificar parâmetros de 31 bits usando o parâmetro AMP=(RMODE31=).

**Tópico pai:**

[Usando o modo de endereçamento de 31 bits com VSAM](https://www.ibm.com/docs/en/SSLTBW_3.1.0/com.ibm.zos.v3r1.idad400/x31bit.htm)