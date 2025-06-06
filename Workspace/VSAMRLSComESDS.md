# Usando VSAM RLS com ESDS

Última atualização : 29/01/2025

O uso de VSAM RLS com ESDSs proporciona maior escalabilidade e disponibilidade em comparação com VSAM não RLS. No entanto, em comparação com VSAM não RLS, o uso de VSAM RLS com ESDSs pode resultar em degradação do desempenho em determinados ambientes operacionais.

Para serializar a adição de registros ESDS no sysplex, o VSAM RLS obtém um bloqueio " add-to-end " *exclusivamente para cada* registro adicionado ao final do conjunto de dados. Se os aplicativos adicionarem registros ao mesmo ESDS com frequência, as solicitações serão processadas em série e, portanto, poderá haver degradação do desempenho.

Em comparação, o VSAM não RLS possui um conjunto diferente de funções e não requer a serialização de adições de registros ESDS no sysplex. Se um ESDS for compartilhado entre threads, planeje cuidadosamente o uso do ESDS com RLS para minimizar qualquer possível impacto no desempenho, em comparação com o uso de ESDSs com VSAM não RLS.



**Observação:** Para VSAM RLS, o sistema obtém um bloqueio global em nível de conjunto de dados apenas para adicionar um registro ESDS ao conjunto de dados, não para ler ou atualizar registros ESDS existentes. Portanto, solicitações GET e atualizações PUT em outros registros dos conjuntos de dados não obtêm o bloqueio " add-to-end " . Essas atualizações podem ser processadas enquanto outra thread mantém o bloqueio " add-to-end " .

A duração do bloqueio " add-to-end " do RLS depende da possibilidade de recuperação do conjunto de dados e do tipo de solicitação PUT que adiciona o registro. Se o conjunto de dados for recuperável, o RLS não libera o bloqueio implicitamente. O bloqueio é liberado explicitamente por ENDREQ, IDAEADD ou IDALKREL. Para conjuntos de dados irrecuperáveis, o comando PUT SEQ libera o bloqueio após a gravação de alguns buffers, enquanto o comando PUT DIR libera o bloqueio ao final da solicitação.

**Tópico pai:**

[Usando o compartilhamento em nível de registro VSAM](https://www.ibm.com/docs/en/SSLTBW_3.1.0/com.ibm.zos.v3r1.idad400/rls.htm)