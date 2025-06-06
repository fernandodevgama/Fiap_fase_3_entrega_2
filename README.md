
# Sistema de Monitoramento Agrícola

Sistema desenvolvido para monitoramento de culturas agrícolas utilizando sensores ESP32 e banco de dados PostgreSQL.

## Equipe
- [Gabriel] (RM: [rm564934])
- [Gabriella Serni Ponzetta] (RM: [rm566296])
- [João Pedro Abreu] (RM: [RM563261])
- [Fernando Ricardo] (RM: [rm566501])
- [João Francisco Maciel Albano] (RM: 565985)

## Estrutura do Projeto

```
.
├── config/
│   ├── database_config.py    # Configurações do banco de dados
├── scripts/
│   ├── migrate.py           # Script de migração do banco
│   └── import_data.py       # Script de importação de dados
├── src/
│   ├── cli.py              # Interface de linha de comando
│   ├── database.py         # Gerenciador do banco de dados
└── README.md
```

## Requisitos

- Python 3.8+
- PostgreSQL 12+
- ESP32
- Sensores:
  - Umidade do solo
  - pH
  - Fósforo
  - Potássio

## Instalação

1. Clone o repositório:
```bash
git clone https://github.com/fernandodevgama/Fiap_fase_3_entrega_2.git
cd Fiap_fase_3_entrega_2
```

2. Instale as dependências:
```bash
pip install -r requirements.txt
```

3. Configure o banco de dados:
- Crie um banco PostgreSQL
- Atualize as configurações em `config/database_config.py`

4. Execute as migrações:
```bash
python scripts/migrate.py
```

5. Importe os dados da planilha CSV:
```bash
python scripts/import_data.py
```
Este script importará automaticamente os dados do arquivo `dados_culturas.csv` que está localizado na pasta `scripts/`. O arquivo CSV deve conter as seguintes colunas:
- nome: Nome da cultura
- tipo: Tipo da cultura
- data_plantio: Data de plantio (formato: YYYY-MM-DD)
- data_colheita_prevista: Data prevista para colheita (formato: YYYY-MM-DD)
- necessidade_agua_min: Necessidade mínima de água (%)
- necessidade_agua_max: Necessidade máxima de água (%)
- necessidade_ph_min: pH mínimo
- necessidade_ph_max: pH máximo
- necessidade_fosforo_min: Fósforo mínimo (g/m²)
- necessidade_fosforo_max: Fósforo máximo (g/m²)
- necessidade_potassio_min: Potássio mínimo (g/m²)
- necessidade_potassio_max: Potássio máximo (g/m²)

O script irá:
1. Ler o arquivo CSV
2. Validar os dados
3. Inserir os registros na tabela `cultura`
4. Exibir um relatório com o número de registros importados

## Uso

1. Inicie o sistema:
```bash
python src/cli.py
```

2. No menu principal, você terá acesso a:
   - Gerenciamento de Culturas
   - Gerenciamento de Lotes
   - Gerenciamento de Sensores
   - Gerenciamento de Leituras
   - Gerenciamento de Ajustes
   - Gerenciamento de Leituras de Solo

## Modelo de Dados Atualizado

### Entidades e Atributos

1. **Cultura**
   - `id_cultura` (PK, INT): Identificador único
   - `nome` (VARCHAR(50)): Nome da cultura
   - `tipo` (VARCHAR(50)): Tipo da cultura
   - `data_plantio` (DATE): Data de plantio
   - `data_colheita_prevista` (DATE): Data prevista para colheita
   - `status` (VARCHAR(20)): Status da cultura
   - `necessidade_agua_min` (NUMERIC(5,2)): Necessidade mínima de água (%)
   - `necessidade_agua_max` (NUMERIC(5,2)): Necessidade máxima de água (%)
   - `necessidade_ph_min` (NUMERIC(4,2)): pH mínimo
   - `necessidade_ph_max` (NUMERIC(4,2)): pH máximo
   - `necessidade_fosforo_min` (NUMERIC(5,2)): Fósforo mínimo (g/m²)
   - `necessidade_fosforo_max` (NUMERIC(5,2)): Fósforo máximo (g/m²)
   - `necessidade_potassio_min` (NUMERIC(5,2)): Potássio mínimo (g/m²)
   - `necessidade_potassio_max` (NUMERIC(5,2)): Potássio máximo (g/m²)

2. **Lote**
   - `id_lote` (PK, INT): Identificador único
   - `id_cultura` (FK, INT): Cultura associada
   - `area` (NUMERIC(10,2)): Área do lote (m²)
   - `localizacao` (VARCHAR(100)): Localização do lote
   - `status` (VARCHAR(20)): Status do lote

3. **Sensor**
   - `id_sensor` (PK, INT): Identificador único
   - `id_lote` (FK, INT): Lote associado
   - `tipo` (VARCHAR(20)): Tipo do sensor
   - `modelo` (VARCHAR(50)): Modelo do sensor
   - `data_instalacao` (DATE): Data de instalação
   - `status` (VARCHAR(20)): Status do sensor

4. **Leitura_Sensor**
   - `id_leitura` (PK, INT): Identificador único
   - `id_sensor` (FK, INT): Sensor associado
   - `data_hora` (TIMESTAMP): Data e hora da leitura
   - `valor` (NUMERIC(10,2)): Valor da leitura
   - `unidade` (VARCHAR(20)): Unidade de medida

5. **Leitura_Solo**
   - `id_leitura_solo` (PK, INT): Identificador único
   - `id_sensor` (FK, INT): Sensor associado
   - `data_hora` (TIMESTAMP): Data e hora da leitura
   - `fosforo_ok` (BOOLEAN): Status do fósforo
   - `potassio_ok` (BOOLEAN): Status do potássio
   - `ph` (NUMERIC(6,2)): Valor do pH
   - `ph_status` (VARCHAR(20)): Status do pH
   - `umidade` (NUMERIC(8,2)): Valor da umidade
   - `umidade_status` (VARCHAR(20)): Status da umidade
   - `irrigacao` (VARCHAR(20)): Status da irrigação

6. **Ajuste**
   - `id_ajuste` (PK, INT): Identificador único
   - `id_lote` (FK, INT): Lote associado
   - `tipo` (VARCHAR(50)): Tipo do ajuste
   - `data_hora` (TIMESTAMP): Data e hora do ajuste
   - `descricao` (TEXT): Descrição do ajuste
   - `status` (VARCHAR(20)): Status do ajuste

## Principais Diferenças do MER Inicial

1. **Cultura**
   - Adicionados campos de tipo, data_plantio, data_colheita_prevista e status
   - Necessidades divididas em valores mínimos e máximos
   - Valores numéricos com precisão definida (NUMERIC)

2. **Lote**
   - Adicionados campos de localizacao e status
   - Renomeado tamanho_lote para area

3. **Sensor**
   - Adicionados campos de modelo, data_instalacao e status
   - Simplificado tipo_sensor para tipo

4. **Leitura_Sensor**
   - Simplificado para armazenar apenas valor e unidade
   - Separado em duas tabelas: Leitura_Sensor e Leitura_Solo

5. **Leitura_Solo (Nova)**
   - Tabela específica para leituras de solo
   - Inclui status para cada medida
   - Campos booleanos para fósforo e potássio
   - Maior precisão para pH e umidade

6. **Ajuste**
   - Simplificado para tipo e descrição
   - Removidos campos específicos de quantidade
   - Adicionado campo de status

## Melhorias Implementadas

1. **Precisão de Dados**
   - Uso de NUMERIC para valores decimais
   - Campos de status para monitoramento
   - Valores booleanos para verificações rápidas

2. **Flexibilidade**
   - Separação de leituras por tipo
   - Ajustes mais genéricos
   - Status em todas as entidades

3. **Monitoramento**
   - Rastreamento de datas
   - Status de operação
   - Histórico de ajustes


## Licença

Este projeto está sob a licença MIT. Veja o arquivo [LICENSE](LICENSE) para mais detalhes. 

