# Evolution Go 0.7.2 no Coolify

Este fork aplica ao handshake do whatsmeow a versão do WhatsApp Web detectada
na primeira conexão do processo, com base no
[PR upstream #227](https://github.com/evolution-foundation/evolution-go/pull/227).
A versão fica fixa até o processo reiniciar. A dependência do whatsmeow permanece
na mesma revisão da tag 0.7.2.

1. Crie uma aplicação a partir do repositório deste fork. Selecione a branch
   `feat/0.7.2-whatsapp-version`, use a estratégia **Docker Compose**, diretório
   base `/` e localização `/compose.coolify.yaml`.
2. Configure `GLOBAL_API_KEY` com a chave administrativa da Evolution Go.
   No PostgreSQL existente, prepare dois bancos lógicos, por exemplo
   `evogo_auth` (sessões do WhatsApp) e `evogo_users` (instâncias e dados da API).
   Configure `POSTGRES_AUTH_DB` e `POSTGRES_USERS_DB` no Coolify com as URLs
   completas dos respectivos bancos, incluindo usuário, senha, host, porta e
   o modo TLS exigido pelo servidor. O usuário precisa poder criar e atualizar
   tabelas nos bancos. Use um endereço acessível a partir do container.
   Se o banco for outro recurso do Coolify na mesma rede privada de destino,
   habilite **Connect to predefined network** e use o endereço interno
   fornecido pelo recurso PostgreSQL. Para as duas URLs e a chave da API,
   deixe **Runtime** habilitado e **Build time** desabilitado. Na visualização
   Developer, use `# coolify: literal buildtime=false` imediatamente acima de
   cada uma dessas variáveis.
3. Deixe `WHATSAPP_VERSION_MAJOR`, `WHATSAPP_VERSION_MINOR` e
   `WHATSAPP_VERSION_PATCH` vazias para detecção automática. Para fixar uma
   revisão conhecida, preencha as três e reinicie a aplicação.
4. Em **Domains**, associe o domínio de homologação ao serviço `evolution-go`,
   com a porta interna `4000`. Exemplo `https://evo-teste.seudominio.com:4000`.
   Se você alterar `SERVER_PORT`, use a mesma porta no domínio do Coolify.
5. Faça o deploy. Se a instalação pedir licença, siga a ativação em `/manager`.
6. Crie uma instância de teste e solicite o QR. Nos logs, procure
   `Applied WhatsApp handshake version`, escaneie o QR e confirme que a
   instância ficou conectada. Se a consulta ao WhatsApp Web falhar na primeira
   conexão, o log mostra a versão usada como fallback. Reinicie o serviço
   para tentar detectar novamente.

Os padrões do Compose seguem a configuração ATiOS. A Evolution Go 0.7.2 lê
`DEBUG_ENABLED` para o nível do whatsmeow e `LOG_TYPE` para o tipo de log.
`WADEBUG` e `LOGTYPE` também são repassadas para facilitar reaproveitar a lista
de variáveis existente.

O health check em `/server/ok` indica que a API está respondendo. Valide a
licença e o pareamento no Manager antes de apontar o SaaS para a instalação.
