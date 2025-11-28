# BRON Dataset to Neo4j Loader

Load the [BRON (Linked Threat Data)](https://github.com/ALFA-group/BRON) dataset into a Neo4j graph database.

BRON links cyber threat intelligence data including:
- **CVE** - Common Vulnerabilities and Exposures
- **CWE** - Common Weakness Enumeration
- **CAPEC** - Common Attack Pattern Enumeration and Classification
- **ATT&CK** - Tactics and Techniques

## Prerequisites

- Python 3.12+
- Neo4j database with [APOC plugin](https://neo4j.com/labs/apoc/) installed
- [uv](https://docs.astral.sh/uv/) (recommended) or pip

## Installation

```bash
# Clone the repository
git clone <repository-url>
cd bron-neo4j

# Install dependencies with uv
uv sync

# Or with pip
pip install -e .
```

## Configuration

Set the following environment variables before running the script:

```bash
export NEO4J_URI="bolt://localhost:7687"
export NEO4J_USER="neo4j"
export NEO4J_PASSWORD="your-password"
export NEO4J_DATABASE="neo4j"  # optional, defaults to "neo4j"
```

Or copy the example environment file and edit it:

```bash
cp .env.example .env
# Edit .env with your credentials
source .env
```

## Usage

Open and run `script.ipynb` in Jupyter:

```bash
uv run jupyter notebook script.ipynb
```

The script will:
1. Download the BRON dataset from GitHub (cached locally in `data/`)
2. Transform nodes and edges into Neo4j-compatible format
3. Load the data into your Neo4j database

## Graph Schema

### Node Labels
- `cve` - Vulnerabilities
- `cwe` - Weaknesses
- `capec` - Attack patterns
- `tactic` - ATT&CK tactics
- `technique` - ATT&CK techniques

### Relationship Types
- `EXPLOITS_WEAKNESS` - CAPEC -> CWE
- `MANIFESTS_AS` - CWE -> CVE
- `INCLUDES_TECHNIQUE` - Tactic -> Technique
- `IMPLEMENTS_PATTERN` - Technique -> CAPEC

## Example Cypher Queries

Find vulnerabilities related to a specific weakness:
```cypher
MATCH (cwe:cwe {original_id: "CWE-79"})<-[:EXPLOITS_WEAKNESS]-(capec)
RETURN cwe, capec
```

Find attack patterns for a specific technique:
```cypher
MATCH (t:technique)-[:IMPLEMENTS_PATTERN]->(capec:capec)
WHERE t.name CONTAINS "Phishing"
RETURN t.name, capec.name
```

## License

MIT
