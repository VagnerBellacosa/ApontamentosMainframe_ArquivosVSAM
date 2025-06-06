# Exemplo 1 para definir e acessar um banco de dados VSAMDB JSON

Última atualização : 2021-04-12

A seguir, um exemplo de alto nível da definição de um arquivo JSON VSAMDB com a chave primária "_id" e a chave AIX "Name". O programa "vsampgm" insere o documento JSON: { "_id" : "00000001" , "Name" : "John Smith" } e, em seguida, recupera o documento por meio da chave alternativa "John Smith". Para um exemplo executável de criação e acesso ao JSON VSAMDB, consulte os Programas de Verificação de Instalação DFPS4IVP em SYS1.SAMPLIB.



```plaintext-ibm
DEFINE CLUSTER (NAME(JSON.KSDS)   -         !  Base cluster:  JSON.KSDS 
        STORCLAS(storclasname)    -         !  RLS storclas with CACHESET                            
        CISZ(26624)               -         !  CISZ round up to sizes which do not span tracks 
        RECORDSIZE(1000,798420)   -         !  Max recordsize always set to 1 Cyl CA (798420)      
        LOG(NONE)                 -         !  RLS eligible, can also be UNDO or ALL     
                                  -         !  Always set to SPANNED by default for VSAMDB                       
        DATABASE(JSON)            -         !  JSON documents to be stored   
        KEYNAME(_id)              -         !  Values for KEYNAME(_id) will be used as the primary key    
                                            !  The internal derived (hashed) record key built from the
                                            !  primary key value is always KEYS(132 0)                         
        INDEXED) ) 
DEFINE ALTERNATEINDEX             -         !  AIX Cluster:  JSON.AIX.KSDS 
       (NAME(JSON.AIX.KSDS)       -
        RELATE(JSON.KSDS)         -                   
        ALTKEYS(Name,A))                    !  The AIX key name is 'Name'. The max-251-byte
                                            !  non-hashed AIX keu value concatenated with a 4-byte block
                                            !  number will be the AIX key value, always KEYS(255 0)
BLDINDEX INDATASET(JSON.KSDS)     -         !  Build the AIX, whether the base document is empty or not
       OUTDATASET(JSON.AIX.KSDS)                 
DEFINE PATH                       -         !  Associates AIX to base
   (NAME(JSON.NAME.PATH)          -                 
        PATHENTRY(JSON.AIX.KSDS))
 
//JSONBASE    DD  DISP=SHR,DSN=JSON.KSDS,RLS=NRI
//JSONPATH    DD  DISP=SHR,DSN=JSON.NAME.PATH,RLS=NRI 
//GO  EXEC  PGM=vsamdpgm
    OPEN    (BASEACB)
    PUT      RPL=OUTRPL
   OPEN     (PATHACB)
   GET       RPL=RETRV
   RETURN
   BASEACB   ACB AM=VSAM,DDNAME=JSONBASE,MACRF=(KEY,DIR,SEQ,RLS,OUT)
   OUTRPL    RPL ACB=BASEACB,AREA=DOC1,OPTCODE=(KEY,DIR,NUP) 
   PATHACB   ACB AM=VSAM,DDNAME=JSONPATH,MACRF=(KEY,DIR,SEQ,RLS,IN)
   RETRVE    RPL ACB=PATHACB,AREA=ARG1,RECLEN=254,                       
        AREALEN=254,OPTCD=(KEY,DIR) 
  
 where DOC1 contains UTF-8 encoding:      
7B225F6964223A223030303030303031222C224E616D65223A224A6F686E20536D697468227D
which is translated to: 
  {  “  _id   “  :  “  00000001   “ , “  Name  “  :  “   John Smith   “  }

ARG1 contains     224E616D65223A224A6F686E20536D69746822          
                   “            John Smith            “
```







**Tópico pai:**

[Definindo e acessando um banco de dados VSAM RLS](https://www.ibm.com/docs/en/SSLTBW_2.4.0/com.ibm.zos.v2r4.idad400/vsamdb.htm)