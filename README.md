# QueryForge
### Natural Language to SQL — Powered by SQLCoder-7B-2
 It's short, memorable, sounds powerful, and perfectly captures the idea of transforming natural language into structured SQL. Works great as a product name, logo, and domain.

<p align="center">
  <img src="https://img.shields.io/badge/Model-SQLCoder--7B--2-blue?style=for-the-badge&logo=huggingface" />
  <img src="https://img.shields.io/badge/Interface-Gradio-orange?style=for-the-badge&logo=gradio" />
  <img src="https://img.shields.io/badge/Python-3.9%2B-green?style=for-the-badge&logo=python" />
  <img src="https://img.shields.io/badge/License-MIT-purple?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Dataset-Spider-red?style=for-the-badge" />
</p>

<p align="center">
  <b>QueryForge</b> converts plain English questions into accurate SQL queries using the open-source <a href="https://huggingface.co/defog/sqlcoder-7b-2">SQLCoder-7B-2</a> model — no API keys, no cloud dependency, runs fully local.
</p>

---

## Demo

> _"Show me the top 5 customers by total order amount"_
> ```sql
> SELECT c.name, SUM(o.total_amount) AS total_spent
> FROM customers c
> JOIN orders o ON c.customer_id = o.customer_id
> GROUP BY c.name
> ORDER BY total_spent DESC
> LIMIT 5;
> ```

---

##  Features

-  **SQLCoder-7B-2** — state-of-the-art open-source Text-to-SQL model (beats GPT-3.5 on Spider benchmark)
-  **Gradio UI** — clean, interactive browser interface
-  **4-bit Quantization** — runs on consumer GPUs (8 GB VRAM) via `bitsandbytes`
-  **Multi-dialect support** — SQL, MySQL, PostgreSQL, SQLite, BigQuery
-  **3 built-in schemas** — E-commerce, University, Hospital
-  **Custom schema support** — paste your own `CREATE TABLE` statements
-  **Spider Dataset** — pre-loaded sample questions from 200+ real databases
-  **CPU fallback** — works without a GPU (slower)

---

##  Project Structure

```
queryforge/
├── sql_query_generator.py   # Main application script
├── requirements.txt         # Python dependencies
├── README.md                # You are here
└── schemas/                 # Optional: custom schema files
    ├── ecommerce.sql
    ├── university.sql
    └── hospital.sql
```

---

##  System Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| Python | 3.9+ | 3.11+ |
| GPU VRAM | 8 GB (4-bit) | 16 GB (full precision) |
| RAM | 16 GB | 32 GB |
| GPU | RTX 3080 | A100 / RTX 4090 |
| CPU (no GPU) |  Supported | — (slow) |

---

##  Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/queryforge.git
cd queryforge
```

### 2. Create a Virtual Environment

```bash
python -m venv venv
source venv/bin/activate        # Linux / macOS
venv\Scripts\activate           # Windows
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Run the App

```bash
python sql_query_generator.py
```

Then open your browser at:
```
http://localhost:7860
```

---

##  Requirements

```txt
torch>=2.0.0
transformers>=4.38.0
accelerate>=0.27.0
bitsandbytes>=0.42.0
gradio>=4.20.0
datasets>=2.17.0
sentencepiece
huggingface_hub
```

Install all at once:
```bash
pip install torch transformers accelerate bitsandbytes gradio datasets sentencepiece huggingface_hub
```

---

##  Dataset — Spider

QueryForge uses the **[Spider Dataset](https://huggingface.co/datasets/xlangai/spider)** (Yale University) for sample schemas and questions.

| Property | Detail |
|----------|--------|
| Questions | 10,000+ natural language questions |
| Databases | 200 real-world databases |
| Domains | E-commerce, healthcare, university, sports, and more |
| SQL complexity | Simple → Nested → Multi-join |
| License | CC BY-SA 4.0 |

It is loaded automatically via HuggingFace Datasets:
```python
from datasets import load_dataset
dataset = load_dataset("xlangai/spider", trust_remote_code=True)
```

---

##  Model — SQLCoder-7B-2

| Property | Detail |
|----------|--------|
| Model | `defog/sqlcoder-7b-2` |
| Base | CodeLlama-7B |
| Fine-tuned on | 100k+ SQL examples |
| Spider accuracy | ~83% (beats GPT-3.5) |
| License | CC BY-SA 4.0 |
| HuggingFace | [defog/sqlcoder-7b-2](https://huggingface.co/defog/sqlcoder-7b-2) |

For higher accuracy at the cost of more VRAM, swap to:
```python
MODEL_NAME = "defog/sqlcoder-34b-alpha"   # 34B version
```

---

##  How to Use

1. **Select a Database** — choose from E-commerce, University, Hospital, or Custom
2. **View the Schema** — the DDL (`CREATE TABLE` statements) loads automatically
3. **Pick a SQL Dialect** — SQL, MySQL, PostgreSQL, SQLite, or BigQuery
4. **Type your Question** — in plain English
5. **Click Generate** — get your SQL query instantly

### Example Questions

**E-commerce schema:**
```
• Show me the top 5 customers by total order amount
• Which products are low in stock (less than 10 units)?
• What is the total revenue per product category?
```

**University schema:**
```
• List all students with GPA above 3.5
• Which course has the most enrollments?
• Show average GPA per major
```

**Hospital schema:**
```
• How many patients were admitted per month this year?
• Which doctor has treated the most patients?
• Find the most common diagnoses
```

---

##  Configuration Options

| Parameter | Default | Description |
|-----------|---------|-------------|
| `MODEL_NAME` | `defog/sqlcoder-7b-2` | HuggingFace model ID |
| `USE_4BIT` | `True` (if GPU) | Enable 4-bit quantization |
| `max_new_tokens` | `300` | Max SQL output length |
| `temperature` | `0.1` | Lower = more deterministic |
| `num_beams` | `4` | Beam search width |
| `server_port` | `7860` | Gradio server port |
| `share` | `False` | Set `True` for public Gradio URL |

---

##  Public Sharing (Optional)

To get a shareable public URL via Gradio tunneling:

```python
demo.launch(share=True)
```

This generates a temporary `https://xxxx.gradio.live` URL valid for 72 hours.

---

##  Docker (Optional)

```dockerfile
FROM python:3.11-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt

COPY sql_query_generator.py .

EXPOSE 7860
CMD ["python", "sql_query_generator.py"]
```

Build and run:
```bash
docker build -t queryforge .
docker run -p 7860:7860 --gpus all queryforge
```

---

##  Troubleshooting

| Issue | Fix |
|-------|-----|
| `CUDA out of memory` | Enable 4-bit quantization or reduce `max_new_tokens` |
| `bitsandbytes not found` | Run `pip install bitsandbytes --upgrade` |
| Model downloads slowly | Use `huggingface-cli login` + `HF_HUB_OFFLINE=1` after first download |
| CPU too slow | Use a smaller model or run on GPU |
| Port 7860 in use | Change `server_port=7861` in `demo.launch()` |

---

##  Roadmap

- [ ] Add query explanation (describe what the SQL does)
- [ ] SQL syntax highlighting in output
- [ ] Query history & export to `.sql` file
- [ ] Upload custom `.sql` schema files
- [ ] SQLite execution sandbox (run and preview results)
- [ ] Support for `defog/sqlcoder-34b-alpha`
- [ ] REST API endpoint alongside Gradio UI

---

##  Contributing

Contributions are welcome!

```bash
# Fork the repo, then:
git checkout -b feature/your-feature-name
git commit -m "Add: your feature description"
git push origin feature/your-feature-name
# Open a Pull Request
```

Please follow [PEP8](https://peps.python.org/pep-0008/) and include docstrings for new functions.

---

##  Acknowledgements

- [Defog AI](https://defog.ai/) — for training and releasing SQLCoder
- [Yale LILY Lab](https://yale-lily.github.io/) — for the Spider dataset
- [HuggingFace](https://huggingface.co/) — for model hosting and the `transformers` library
- [Gradio](https://gradio.app/) — for the UI framework

---

<p align="center">
  Built with ❤️ using SQLCoder-7B-2 & Gradio &nbsp;|&nbsp; ⭐ Star this repo if you find it useful!
</p>
