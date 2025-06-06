# Exemplo 2 para definir e acessar um banco de dados VSAMDB BSON

Última atualização : 2021-04-12

A seguir, um exemplo de alto nível da definição de um arquivo BSON VSAMDB com a chave primária "_id" e a chave AIX "Email". O programa "vsampgm" insere o documento equivalente a BSON {"_id":"01","Name":"John","Email":"John@us.ibm.com"} e recupera o documento por meio da chave alternativa {"Email":"John@us.ibm.com"}.



**Observação:** Para facilitar a comparação entre os exemplos JSON e BSON, o formato do documento fornecido e o argumento da solicitação aqui estão em JSON. Consulte BSONspec.org para a definição dos elementos em um documento BSON. Para um exemplo executável de criação e acesso ao banco de dados BSON VSAMDB, consulte os Programas de Verificação de Instalação DFPS5IVP em SYS1.SAMPLIB.

```plaintext-ibm
DEFINE CLUSTER (NAME(BSON.KSDS)   -         !  Base cluster:  BSON.KSDS 
        STORCLAS(storclasname)    -         !  RLS storclass with CACHESET                            
        CISZ(26624)               -         !  CISZ round up to sizes which do not span tracks 
        RECORDSIZE(1000,798420)   -         !  Max recordsize is always set to 1-cylinder CA       
        LOG(NONE)                 -         !  RLS eligible, can also be UNDO or ALL     
        SPANNED                   -         !  Set by default for VSAMDB                       
        DATABASE(BSON)            -         !  BSON documents to be stored   
        KEYNAME(_id)              -         !  Values for KEYNAME(_id) will be used as the primary key   
                                            !  The internal derived (hashed) record key built from the
                                            !  primary key value is always KEYS(132 0)                        
        INDEXED) ) 
DEFINE ALTERNATEINDEX             -         !  AIX Cluster:  BSON.AIX.KSDS 
       (NAME(BSON.AIX.KSDS)       - 
        RELATE(BSON.KSDS)         -                   
        ALTKEYS(Name,A))                    !  The AIX key name is 'Name'. The max-251-byte
                                            !  non-hashed AIX key value appended with a 4-byte block
                                            !  number will be the AIX key value, always KEYS(255 0)
BLDINDEX INDATASET(BSON.KSDS)     -         !  Build the AIX, whether the base document is empty or not
       OUTDATASET(BSON.AIX.KSDS)                 
DEFINE PATH                       -         ! Associates AIX to base
   (NAME(JSON.NAME.PATH)          -                 
               PATHENTRY(BSON.AIX.KSDS))

//BSONBASE    DD  DISP=SHR,DSN=BSON.KSDS,RLS=NRI
//BSONPATH    DD  DISP=SHR,DSN=BSON.NAME.PATH,RLS=NRI 
//GO  EXEC  PGM=Vsamdpgm
    OPEN     (BASEACB)
    PUT       RPL=OUTRPL
   OPEN      (PATHACB)
   GET        RPL=RETRV
   RETURN
   BASEACB   ACB AM=VSAM,DDNAME=BSONBASE,MACRF=(KEY,DIR,SEQ,RLS,OUT)
   OUTRPL    RPL ACB=BASEACB,AREA=DOC1,OPTCODE=(KEY,DIR,NUP) 
   PATHACB    ACB AM=VSAM,DDNAME=BSONPATH,MACRF=(KEY,DIR,SEQ,RLS,IN)
   RETRVE    RPL ACB=PATHACB,AREA=ARG1,RECLEN=59,                       
        AREALEN=59,OPTCD=(KEY,DIR)  

   DOC1  DC XL59'3b000000025f69640003000000303100024e616d6500050000004a*
                6f686e0002456d61696c00100000004a6f686e4075732e69626d2e63*
                6f6d0000'                                                   
   which is BSON in UTF-8 encoding, equivalent to JSON format
      {"_id":"01","Name":"John","Email":"John@us.ibm.com"}

   ARG1      '02456d61696c00100000004a6f686e4075732e69626d2e636f6d00          
   which is BSON in UTF-8 encoding, equivalent to JSON format {"Email":"John@us.ibm.com"}
```







**Tópico pai:**

[Definindo e acessando um banco de dados VSAM RLS](https://www.ibm.com/docs/en/SSLTBW_2.4.0/com.ibm.zos.v2r4.idad400/vsamdb.htm)