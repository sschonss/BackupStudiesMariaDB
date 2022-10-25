# Bakcup's Maria DB

## RTO (Recovery Time Objective)

A quantidade de tempo que o sistema pode ficar fora do ar sem que isso afete a empresa.

Deve ser levado em consideração o tempo de recuperação do banco de dados, o tempo de recuperação do sistema e o tempo de recuperação do usuário.

Isso pode ser reduzido com a replicação de banco de dados e balanceamento de carga.

Foi dada a opção de failover, que é a troca de um servidor por outro, mas isso não é uma solução definitiva. NECESSITA DE ESTUDO.

Outro o exemplo de opção foi SkySQL, que é um serviço de banco de dados em nuvem. NECESSITA DE ESTUDO.

## RPO (Recovery Point Objective)

Duração de tempo e nivel de serviço dentro do qual um processo da regra de negócio deve ser restaurado após uma falha para evitar consequências inaceitáveis associadas ao processo.

Perguntas recorrentes:
 - Quantos dados eu posso perder?
 - Qual foi o ultimo backup antes da falha?


## Objetivos

O objetivo de uma empresa sempre é ter um RTO e RPO baixo, mas isso não é possível. O objetivo é ter um RTO e RPO aceitável.
Necessário investir em redundância e backups.

---

# Risk Mitigation

### Em quais cenários eu preciso estar preparado?

- Pode ajudar a mitigar cenários de falhas:
  - Falha de Host
  - Falha no Datacenter
  - Dados corrompidos ou perdidos
- Atender aos Regulamentos Legais:
  - LGPD
  - Regulamentos específicos de cada contrato
- Ordem de recuperação:
  - Qual eu recupero primeiro? 
    - O banco de dados ou o sistema?

---

# Retention 

## Classico

Existem diversos tipos de retencao, mas o mais comum ainda é o classico.

O classico pode ser feito de varias maneiras:
 - Backup semanal no servidor local
 - Backup mensal no servidor local
 - Backup remoto
 - e etc

## Futuro

No futuro o backup pode ser feito de forma mais automatizada, com o uso de ferramentas de backup e recuperação.
 - Armazenamento em nuvem.
 - Backup em nuvem.
 - e etc

---

# Continuidade de Negócio com Backup

Existem 3 tipos de backup:
 - Full
 - Incremental
 - Diferencial

---
## Full

O backup full é o backup completo do banco de dados. 

Existem alguns metodos de backup full:

### Physical
- Uma cópia do banco de dados é feita em disco.
- Usado para mitigar uma falha de host unico, normalmente.
- Pode construir réplicas de banco de dados.
- Tempo total de recuperação total é relativamente rápido.
- Lento para restaurar dados individuais, como uma linha ou tabela.
- MariaDB Enterprise Backup

### Logical
- Gera arquivos SQL que podem ser usados para restaurar o banco de dados.
- Restaure uma linha ou tabela mais rapidamente.
- Processo de restauracao replicado automaticamente.
- Tempo total de recuperação total é muito lento.
- MariaDB Dump
- MyDump

### Block Level
- Pode ser remontado em segundos.
- Binlogs fazem parte do snapshot, então é possível PITR (Point In Time Recovery) ainda é possível.
- Cripitografia padrao do provedor de nuvem.
- O armazenamento é em bloco na nuvem tem mais de 99% de durabilidade.
- Storage Snapshots

--- 

## Restauraçao parcial de backups fisicos (Physical Backups)

- Voce pode importar uma tabela de um MariaDB Backup feito com --export
- Pode nao ser consistente com dados de outras tabelas.
- Pode ser usado no SkySQL.

---

## Incremental e Diferencial

- Informações necessárias para seguir a documentação:
  - Incremental: Backup de apenas as alterações desde o ultimo backup.
  - Diferencial: Backup de apenas as alterações desde o ultimo backup full.

--- 

## Recomendações de Backup Incremental e Diferencial

- Realizar backup full semanalmente, incremental diariamente e diferencial mensalmente.
- Normalmente o backup full é feito no domingo.
- O backup incremental é feito de segunda a sabado.
- O backup diferencial é feito no primeiro dia do mes.

---

## Tempo de Backup

- O tempo de backup depende do tamanho do banco de dados.
- Normalmente o backup full demora mais tempo.
- O backup incremental demora menos tempo.
- O backup diferencial fica entre o full e o incremental.

---

## Recuperação de Backup

- Recuperar um backup full é mais rápido.
- Recuperar um backup incremental é mais lento.
- Recuperar um backup diferencial é mais lento que o incremental.

---

## Backups necessitam de testes

- Teoricamente nenhum backup é 100% confiavel.
- É necessário testar os backups periodicamente.
- Nenhum backup pode ser disponibilizado sem testes.

---

## Teste de recuperação ideal

- Realizar o teste de recuperação em um ambiente de teste semelhante ao de produção.
- Testes nos sistemas devem ser realiazados para que não haja problemas de integração.
- Garantir a disponibilidade de chaves e senhas criptografadas.

---

# Recuperaçoes baseadas em pontos de tempo (PITR)

## Logs Binários

- Enderecos RPO (Recovery Point Objective)
- Os logs devem ser rotacionados e armazenados em backup
- Se voce fizer backup dos logs a cada uma hora, seu RPO sera de uma hora em caso de falha.
- Esse tempo pode ser reduzido, mas nao ao ponto de interferir no trafego de entrada e saida de rede e nem interfira na performance do banco.
- Adicionar mais complexidade aos processos de backup deixa a recuperação mais lenta. Sempre busque o equilibrio.

---

## Replicas atrasadas

- Replica atrasada é uma replica de um banco de dados que esta atrasada em relação ao banco de dados original.
- Atraso pode ser de minutos, horas ou dias.
- Replica atrasada é usada para recuperar dados perdidos ou corrompidos.
- Uma replica atrasada deve ficar atrás do banco de dados original, em pelo menos um tempo especifico.

---

## Tipos de replicas atrasadas

- A inclusao de dados em uma replica atrasada é feita por meio de logs binários.
- A incluir mutiplos intervalos de tempo permite uma recuperação mais precisa e rapida.
  - Uma réplica com atraso de uma hora é crítica quando os problemas podem ser encontrados rapidamente e o avanço de uma réplica com atraso de 24 horas pode demorar mais do que o RTO permite.
  - Muitas vezes, um erro de dados ou outra forma de corrupção não é detectado na primeira hora, o que torna a réplica atrasada de 24 horas crítica.
  - Outra opção é dividir a diferença e substituir ambas por uma réplica atrasada de 6 a 12 horas.
  - Uma réplica atrasada de vários dias também é crítica nos casos em que um erro pode não ser fatal e pode ser detectado muito mais tarde. Nesses casos, torna-se necessário recuperar dados de dias atrás. Outro exemplo dessa necessidade é uma falha na sexta-feira que não é detectada durante o final de semana e só é detectada na segunda-feira.

--- 

## SkySQL

- O SkySQL é um serviço de banco de dados gerenciado que oferece uma solução de banco de dados de alta disponibilidade e alta performance.
