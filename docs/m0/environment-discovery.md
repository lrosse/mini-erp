# TEMPORÁRIO — M0-T01 Environment Discovery

Este relatório é temporário e será consolidado na M0-T11. Foi produzido em 2026-10-02, somente com comandos de leitura local e consulta a documentação oficial. Nenhuma instalação, alteração de feature, login de registry, download de imagem ou container foi realizado nesta task.

## 1. Resumo do ambiente

### Sistema, hardware e caminho

- Windows 11 Home Single Language, 64-bit, build 26200.
- PowerShell 7.6.5 e Windows PowerShell 5.1 disponíveis; Bash, WSL e CMD também disponíveis.
- Intel Core i5-12500H, 12 cores e 16 threads.
- RAM total de 15,69 GB; aproximadamente 2,60 GB livres durante a medição.
- Unidade C: NTFS, SSD NVMe, aproximadamente 126 GB livres na validação final.
- O caminho confirmado pelo Product Owner é `<PROJECT_PATH>`; a pasta existe, estava vazia, não tinha `.git`, não contém espaços nem caracteres não ASCII e não apresentou marcador local conhecido de sincronização. O caminho tem comprimento curto e não há sinal de filesystem inadequado. A confirmação efetiva de permissões ocorrerá pelo próprio `git init` e pelo commit desta task.

### Virtualização e WSL

- WSL está instalado e reporta versão 2.7.3, com kernel WSL2 ativo.
- A única distribuição reportada foi `docker-desktop`, parada, em WSL2.
- O hypervisor foi detectado como presente, mas a consulta WMI também retornou a flag de virtualização no firmware como desabilitada. O resultado é conflitante e pode ser falso negativo.
- A consulta das features opcionais do Windows não foi concluída: DISM respondeu com erro 740, exigindo elevação. Nenhuma elevação foi tentada.
- Existe um `.wslconfig`, mas não foram identificados nos parâmetros consultados limites explícitos de memória, CPU ou swap.
- Classificação: NEEDS CONFIGURATION. A confirmação real ficará para a M0-T03, ao iniciar o daemon do Docker.

### Containers

- Docker Desktop está instalado; Docker CLI 29.4.3 e Docker Compose v5.1.3 estão disponíveis.
- Os contextos `default` e `desktop-linux` existem; o contexto Linux é o selecionado, mas o daemon não estava acessível e o processo do Docker Desktop não estava ativo durante a medição.
- Imagens, containers e volumes existentes não puderam ser contados porque o daemon estava parado. Não foram listados nomes de projetos.
- Não foi detectado Podman nem nerdctl.
- O backend pretendido é Linux/WSL2. Windows Home não suporta containers Windows segundo a documentação do Docker.
- A localização padrão documentada do disco virtual do Docker Desktop é no perfil local do usuário; a presença/uso efetivo não foi confirmada porque o daemon estava parado.

### Ferramentas

- Git 2.53.0; identidade local/global está definida, sem expor nome ou e-mail.
- `core.autocrlf` e `core.eol` não estão definidos.
- GitHub CLI 2.90.0 está instalado e autenticado; nenhuma credencial foi registrada neste documento.
- Java Temurin/OpenJDK 17.0.18 está disponível no PATH; `JAVA_HOME` não está definido.
- Python 3.13.14 está disponível.
- SQLcl, SQL*Plus, `lsnrctl`, Oracle Client, `ORACLE_HOME` e `TNS_ADMIN` não foram detectados.
- VS Code e Notepad estão disponíveis.
- `winget`, Chocolatey e Scoop não foram detectados pelos comandos de versão.

### Rede e portas

- As portas TCP 1521, 5500, 8080, 8181, 8443, 3000, 5432, 1433 e 9000 estavam livres.
- Há listeners do Windows e de software instalado em outras portas, mas nenhum conflito nas portas-alvo foi encontrado.
- Faixas excluídas observadas pelo Windows: 5357, 8883–8886 e 50000–50059. A consulta foi feita sem elevação; somente as faixas retornadas pelo sistema foram registradas.
- Não foi detectado proxy no WinHTTP, nas configurações de proxy do usuário ou nas variáveis de ambiente consultadas.
- Conectividade TCP/443 foi confirmada para `container-registry.oracle.com` e `github.com`.

## 2. Requisitos oficiais consultados

Data de consulta: 2026-10-02.

### Oracle AI Database 26ai Free

- A documentação oficial informa 1 GB de RAM mínimo, 2 GB recomendados e 10 GB de disco mínimo. Também recomenda pelo menos 2 GB de swap ou duas vezes a RAM, o que for menor.
- A edição Free limita o processamento a 2 cores, a RAM do banco a 2 GB, os dados de usuário a 12 GB e a uma instalação por ambiente lógico. Um container conta como ambiente lógico.
- O repositório oficial de imagens documenta a imagem Free 26ai e o uso de volume persistente em `/opt/oracle/oradata`; o listener usa a porta 1521 e a imagem expõe também o endpoint de administração indicado pela imagem.
- A página oficial de downloads fornece o pull da imagem `container-registry.oracle.com/database/free:latest`. O uso de tag flutuante não é recomendado para o projeto; deve ser substituído por tag/digest fixo quando a M0-T03 for executada.

Fontes:

- [Oracle AI Database Free — requisitos](https://docs.oracle.com/en/database/oracle/oracle-database/26/xeinl/requirements.html)
- [Oracle AI Database Free — limitações](https://docs.oracle.com/en/database/oracle/oracle-database/26/xeinw/licensing-restrictions.html)
- [Oracle AI Database — informações de licenciamento](https://docs.oracle.com/en/database/oracle/oracle-database/26/dblic/Licensing-Information.html)
- [Oracle Docker Images — Single Instance](https://github.com/oracle/docker-images/blob/main/OracleDatabase/SingleInstance/README.md)
- [Oracle Database — downloads oficiais](https://www.oracle.com/database/technologies/oracle-database-software-downloads.html)

### Registry Oracle

- A documentação oficial do Oracle Container Registry informa que imagens Oracle licenciadas exigem conta Oracle, aceite dos termos no repositório e autenticação do cliente com `docker login` ou equivalente.
- A edição Database Free possui os Oracle Free Use Terms; porém a aceitação efetiva do repositório `database/free` não foi testada, pois esta task proíbe login e pull. Deve ser verificada na M0-T03 antes do download.

Fonte: [Oracle Container Registry — pulling images](https://docs.oracle.com/en/operating-systems/oracle-linux/docker/docker-UsingDockerRegistries.html).

### APEX e ORDS

- APEX não está incluído na edição Free; deve ser baixado e instalado separadamente.
- APEX 26.1 requer ORDS 26.1.1 ou posterior. A documentação recomenda instalar ORDS antes do APEX em novas instalações.
- ORDS é separado do banco: pode ser obtido por ZIP/RPM ou por imagem própria no Oracle Container Registry. A imagem de ORDS inclui GraalVM e uma distribuição de produção do ORDS.
- Para APEX com ORDS é necessário instalar o ZIP do APEX, disponibilizar os recursos estáticos e configurar o PL/SQL Gateway. O ZIP do APEX e seus checksums não pertencem ao repositório Git público.

Fontes:

- [Oracle AI Database Free — componentes não incluídos](https://docs.oracle.com/en/database/oracle/oracle-database/26/dblic/Licensing-Information.html)
- [APEX 26.1 — requisitos](https://docs.oracle.com/en/database/oracle/apex/26.1/htmig/apex-installation-requirements.html)
- [ORDS 26.1 — instalação e configuração](https://docs.oracle.com/en/database/oracle/oracle-rest-data-services/26.1/ordig/installing-and-configuring-oracle-rest-data-services.html)
- [APEX — instalação de APEX e ORDS](https://docs.oracle.com/en/database/oracle/apex/26.1/htmig/installing-and-configuring-apex-and-ords.html)

### Docker Desktop e WSL2

- O Docker documenta WSL 2.1.5 ou posterior, 8 GB de RAM de sistema e virtualização de hardware habilitada como pré-requisitos do backend WSL2.
- Windows Home permite somente containers Linux.
- Para desempenho de bind mounts, o Docker recomenda manter arquivos montados em containers Linux no filesystem Linux. Para o banco, a regra desta task é mais restritiva: usar volume nomeado do Docker e nunca bind mount para pasta Windows.

Fontes:

- [Docker Desktop no Windows](https://docs.docker.com/desktop/setup/install/windows-install/)
- [Docker Desktop com backend WSL2](https://docs.docker.com/desktop/features/wsl/)
- [Docker — boas práticas WSL2 e bind mounts](https://docs.docker.com/desktop/features/wsl/best-practices/)

### SQLcl

- A documentação atual do SQLcl informa Java 17 ou 21; o Java 17 existente é compatível.
- SQLcl é distribuído separadamente como ZIP e ainda não existe no ambiente local.

Fonte: [Oracle SQLcl — download e instalação](https://docs.oracle.com/en/database/oracle/apex/26.1/aeadm/downloading-and-installing-sqlcl.html).

## 3. Classificação da Fase 5

| Requisito | Classificação | Motivo e ação sugerida |
|---|---|---|
| Oracle AI Database 26ai Free | NEEDS CONFIGURATION | CPU, disco e RAM total são suficientes nominalmente, mas há só 2,6 GB livres, o daemon está parado e a virtualização precisa ser confirmada na M0-T03; medir o conjunto Docker/WSL2/Oracle antes de prosseguir. |
| Oracle APEX | NEEDS INSTALLATION | APEX é separado da Free e não está instalado; escolher ZIP versionado, checksum fixo e cache fora do Git. |
| ORDS | NEEDS INSTALLATION | Não está instalado; instalar uma versão compatível com APEX 26.1, no mínimo 26.1.1, preferencialmente fixada por versão/digest. |
| Runtime de containers + Compose | NEEDS CONFIGURATION | Docker Desktop e Compose estão instalados, mas o daemon não está ativo; confirmar backend Linux/WSL2 e limites na M0-T03. |
| SQLcl + Java compatível | NEEDS INSTALLATION | Java 17 está compatível, mas SQLcl não existe; instalar versão fixada quando autorizado. |
| PL/SQL | NEEDS CONFIGURATION | Não há Oracle local; PL/SQL será executado no Oracle container via SQLcl, com edição em arquivos SQL/PLSQL e editor local. |
| utPLSQL | NEEDS INSTALLATION | Não foi detectado; instalar posteriormente no banco a partir de release fixada, conforme task própria. |
| Migrations | NEEDS DECISION | Ainda não foi decidido entre Liquibase via SQLcl e SQL numerado com runner simples; preservar ambas como experimento da M0. |
| Git e GitHub CLI | READY | Git, identidade e GitHub CLI autenticado estão disponíveis; aplicar `.gitattributes` na M0-T02 para fixar finais de linha. |
| CI — GitHub Actions | READY | Não exige runner local; GitHub CLI autenticado e Java/Python disponíveis, mas a ativação depende do repositório remoto e de task futura. |
| Linguagem de scripts de dev | NEEDS DECISION | PowerShell 7 e Bash estão disponíveis; recomendação: PowerShell para orquestração local Windows e Bash para compatibilidade com CI Linux, sem decidir nesta task. |

## 4. Riscos, conflitos e questões em aberto

- **RAM:** 2,6 GB livres na medição, contra a necessidade combinada do Oracle Free, ORDS e VM WSL2. A capacidade nominal da máquina não garante margem operacional; medir o pico real na M0-T03.
- **Virtualização:** a flag do firmware retornou resultado conflitante com hypervisor ativo e WSL2 instalado. Tratar como NEEDS CONFIGURATION e confirmar ao subir o daemon, sem alterar firmware nesta task.
- **APEX:** não vem com a Free e não pode ser commitado. O setup futuro deve baixar ZIP com versão e checksum fixos para cache ignorado pelo Git.
- **Compatibilidade:** APEX 26.1 exige ORDS 26.1.1 ou superior.
- **Limite de dados:** permanece em aberto se o schema/objetos internos do APEX contam, na prática, contra o limite de 12 GB de dados de usuário da Free; medir e documentar na instalação.
- **Finais de linha:** `core.autocrlf` não está definido; criar política explícita em `.gitattributes` na M0-T02.
- **Persistência:** na M0-T03, dados do banco devem usar volume nomeado do Docker, nunca bind mount para pasta do Windows.
- **Portas:** nenhuma porta-alvo está ocupada; as faixas excluídas pelo Windows devem ser consideradas ao escolher portas alternativas.
- **Caminho:** o caminho confirmado é curto, local, NTFS, sem espaços/caracteres não ASCII e com SSD; não há conflito conhecido. Bind mounts de código Windows em containers Linux podem ter desempenho inferior, conforme Docker; isso não se aplica ao volume nomeado do banco.
- **Registry/rede:** conectividade foi confirmada, mas login, aceite de termos e pull não foram testados por restrição da task.

Questões que afetam arquitetura:

1. Migrations: Liquibase via SQLcl oferece ferramenta declarativa, mas adiciona dependência e complexidade; SQL numerado com runner simples reduz dependências, mas exige convenções e controle próprios. A decisão fica para o experimento da M0.
2. APEX/ORDS: usar containers separados ou uma composição controlada é compatível com a documentação; a escolha final deve considerar memória, atualização e persistência sem embutir binários no repositório.
3. Imagem Oracle: usar tag flutuante simplifica atualização, enquanto tag/digest fixo melhora reprodutibilidade; recomenda-se fixar para o projeto público.

## 5. Recomendações

- Na M0-T03, iniciar o Docker Desktop somente quando autorizado, confirmar virtualização pelo funcionamento do daemon e medir memória livre antes/depois de cada serviço.
- Fixar versão/digest da imagem Oracle, ORDS e APEX; registrar checksums e manter downloads em cache ignorado pelo Git.
- Usar volume nomeado para `/opt/oracle/oradata`; não armazenar dados do banco em bind mount Windows.
- Adicionar `.gitattributes` na M0-T02 para tornar finais de linha determinísticos.
- Recomendar PowerShell 7 para automação local no Windows e Bash para scripts executados no CI Linux; deixar a decisão formal para o arquiteto.

## 6. Validação desta task

- Nenhum software, feature do SO, imagem ou container foi instalado, criado, baixado ou iniciado.
- Nenhuma configuração do sistema, PATH, variável de ambiente, registro ou Docker foi alterada.
- O documento foi revisado para não conter nome de usuário do SO, hostname, e-mail, IP, número de série, token, valor de variável de ambiente, PATH pessoal ou nome de outro projeto.
- O único artefato de trabalho é este documento, além dos metadados normais do Git necessários para o commit.
