# TOON Newsletter: Cut Your LLM Costs by 30-60% with Smarter Data Formatting

## 🚀 The Hook: What if You Could Slash Your LLM Token Usage in Half?

Imagine feeding the same data to your LLM but consuming **30-60% fewer tokens** while actually **improving accuracy**. No model changes. No prompt engineering tricks. Just a smarter way to format your data.

Meet **TOON (Token-Oriented Object Notation)** — the data format designed specifically for LLM prompts that's changing how developers think about token efficiency.

---

## 🎯 What is TOON?

**TOON (Token-Oriented Object Notation)** is a compact, human-readable encoding format that represents the same JSON data model but is optimized specifically for Language Model input. Think of it as JSON's efficient cousin — it encodes the same objects, arrays, and primitives but does so with far fewer tokens.

### Key Characteristics:

- **Lossless JSON Representation**: Deterministic round-trips between JSON and TOON
- **Token-Optimized**: Combines YAML's indentation-based structure with CSV-style tabular layouts
- **LLM-Friendly**: Explicit array lengths `[N]` and field headers `{fields}` provide clear schema guidance
- **Minimal Syntax**: No curly braces, square brackets, or quotes where unnecessary
- **Human-Readable**: Still easy to read and understand, unlike binary formats

### How It Works:

TOON achieves its efficiency by:
1. **Using indentation** instead of braces and brackets
2. **Collapsing uniform arrays** into compact tabular formats (like CSV)
3. **Removing redundant punctuation** while maintaining structure
4. **Providing explicit schema hints** that help LLMs parse data more accurately

---

## 📊 The Comparison: Natural Language vs JSON vs TOON

Let's compare three approaches to feeding the same data to an LLM using a real-world example: a list of hiking trails.

### Example Data:
3 hiking trails with ID, name, distance, and difficulty level.

### **Approach 1: Natural Language Description**

```text
Here's a list of hiking trails:
- The first trail has ID 1, named "Blue Lake Trail", is 7.5 kilometers long, and has easy difficulty
- The second trail has ID 2, named "Ridge Overlook", is 9.2 kilometers long, and has moderate difficulty
- The third trail has ID 3, named "Summit Peak", is 12.8 kilometers long, and has hard difficulty
```

**Token Count**: ~75 tokens
**Accuracy**: ~65% (varies by model, lacks structure)
**Issues**: Verbose, ambiguous parsing, no schema validation

---

### **Approach 2: Standard JSON**

```json
{
  "hikes": [
    {
      "id": 1,
      "name": "Blue Lake Trail",
      "distanceKm": 7.5,
      "difficulty": "easy"
    },
    {
      "id": 2,
      "name": "Ridge Overlook",
      "distanceKm": 9.2,
      "difficulty": "moderate"
    },
    {
      "id": 3,
      "name": "Summit Peak",
      "distanceKm": 12.8,
      "difficulty": "hard"
    }
  ]
}
```

**Token Count**: ~84 tokens (formatted with 2-space indentation)
**Accuracy**: ~69.7%
**Issues**: Repetitive field names, excessive punctuation, higher token cost

---

### **Approach 3: TOON Format**

```toon
hikes[3]{id,name,distanceKm,difficulty}:
 1,Blue Lake Trail,7.5,easy
 2,Ridge Overlook,9.2,moderate
 3,Summit Peak,12.8,hard
```

**Token Count**: ~28 tokens
**Accuracy**: ~73.9%
**Benefits**: Compact, structured, explicit schema, CSV-like efficiency

---

### **Comparison Summary Table**

| Format | Token Count | Token Savings | Accuracy | Readability | Schema Awareness |
|--------|-------------|---------------|----------|-------------|------------------|
| **Natural Language** | ~75 | Baseline | ~65% | High | None |
| **JSON (formatted)** | ~84 | -12% (more tokens) | ~69.7% | High | Implicit |
| **TOON** | ~28 | **63% savings** | **~73.9%** | High | **Explicit** |

### Real-World Impact:

For larger datasets, the savings compound dramatically:

- **100 uniform records**: JSON = ~2,800 tokens, TOON = ~900 tokens (**67% reduction**)
- **1,000 uniform records**: JSON = ~28,000 tokens, TOON = ~9,000 tokens (**67% reduction**)

**Cost Impact**: If you're spending $1,000/month on LLM API costs with structured data, TOON could save you **$300-$600/month**.

---

## 🛠️ How to Use TOON in Your Project

### **Step 1: Choose Your Implementation**

TOON has official libraries for multiple languages:

#### **TypeScript/JavaScript (Node.js)**
```bash
npm install @toon-format/toon
```

#### **Python**
```bash
pip install toon-format
# OR
pip install python-toon
```

#### **.NET/C#**
```bash
dotnet add package Toon.Format
```

---

### **Step 2: Basic Usage Examples**

#### **TypeScript Example**

```typescript
import { encode, decode } from '@toon-format/toon';

// Your existing JSON data
const jsonData = {
  users: [
    { id: 1, name: "Alice", role: "admin" },
    { id: 2, name: "Bob", role: "user" },
    { id: 3, name: "Charlie", role: "user" }
  ]
};

// Convert to TOON format
const toonString = encode(jsonData);
console.log(toonString);
// Output:
// users[3]{id,name,role}:
//  1,Alice,admin
//  2,Bob,user
//  3,Charlie,user

// Use in your LLM prompt
const prompt = `Given this user data:\n${toonString}\n\nList all admin users.`;

// Convert back to JSON if needed
const decoded = decode(toonString);
console.log(decoded); // Original JSON object
```

#### **Python Example**

```python
from toon import encode, decode

# Your existing Python dict
data = {
    "products": [
        {"id": 101, "name": "Laptop", "price": 999.99},
        {"id": 102, "name": "Mouse", "price": 29.99},
        {"id": 103, "name": "Keyboard", "price": 79.99}
    ]
}

# Convert to TOON
toon_str = encode(data)
print(toon_str)
# Output:
# products[3]{id,name,price}:
#  101,Laptop,999.99
#  102,Mouse,29.99
#  103,Keyboard,79.99

# Use in your LLM prompt
prompt = f"Given this product catalog:\n{toon_str}\n\nRecommend products under $100."

# Decode back to Python dict
decoded_data = decode(toon_str)
```

---

### **Step 3: Integration with LLM APIs**

#### **OpenAI API Example**

```typescript
import OpenAI from 'openai';
import { encode } from '@toon-format/toon';

const client = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });

const data = {
  transactions: [
    { date: "2025-01-15", amount: 250.00, category: "groceries" },
    { date: "2025-01-16", amount: 50.00, category: "transport" },
    { date: "2025-01-17", amount: 1200.00, category: "rent" }
  ]
};

// Encode data as TOON
const toonData = encode(data);

// Send to LLM with fewer tokens
const response = await client.chat.completions.create({
  model: "gpt-4",
  messages: [
    {
      role: "user",
      content: `Analyze these transactions and provide insights:\n${toonData}`
    }
  ]
});

console.log(response.choices[0].message.content);
```

#### **Anthropic Claude API Example**

```python
import anthropic
from toon import encode

client = anthropic.Anthropic(api_key="your-api-key")

data = {
    "logs": [
        {"timestamp": "2025-01-15T10:00:00Z", "level": "ERROR", "message": "Connection timeout"},
        {"timestamp": "2025-01-15T10:05:00Z", "level": "WARN", "message": "Retry attempt 1"},
        {"timestamp": "2025-01-15T10:10:00Z", "level": "INFO", "message": "Connection restored"}
    ]
}

toon_logs = encode(data)

message = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    max_tokens=1024,
    messages=[
        {
            "role": "user",
            "content": f"Analyze these system logs:\n{toon_logs}\n\nWhat issues do you see?"
        }
    ]
)

print(message.content)
```

---

### **Step 4: Token Analysis (Optional)**

Measure your actual token savings:

```typescript
import { encode } from '@toon-format/toon';
import { encode as tiktoken } from 'gpt-tokenizer';

const data = { /* your data */ };

// Compare token counts
const jsonString = JSON.stringify(data, null, 2);
const toonString = encode(data);

const jsonTokens = tiktoken(jsonString).length;
const toonTokens = tiktoken(toonString).length;

console.log(`JSON tokens: ${jsonTokens}`);
console.log(`TOON tokens: ${toonTokens}`);
console.log(`Savings: ${((1 - toonTokens/jsonTokens) * 100).toFixed(1)}%`);
```

---

### **Step 5: Best Practices**

✅ **Use TOON for:**
- Large uniform arrays of objects (user lists, transaction logs, product catalogs)
- Structured data with repeated fields
- RAG systems with document metadata
- Analytics and reporting data
- API response data feeding into LLMs

❌ **Avoid TOON for:**
- Deeply nested or highly irregular data structures
- Single objects (no arrays)
- Pure tabular data with no nesting (use CSV instead)
- Latency-critical applications where JSON parsing speed matters

---

## 🔗 Important Links & Resources

### **Official Resources**

- **Official Website**: [toonformat.dev](https://toonformat.dev)
- **GitHub Repository**: [github.com/toon-format/toon](https://github.com/toon-format/toon)
- **Full Specification**: [github.com/toon-format/spec](https://github.com/toon-format/spec)
- **NPM Package**: [@toon-format/toon](https://www.npmjs.com/package/@toon-format/toon)

### **Language-Specific Implementations**

- **Python Implementation**: [github.com/xaviviro/python-toon](https://github.com/xaviviro/python-toon)
- **Python PyPI Package**: [pypi.org/project/toon-format](https://pypi.org/project/toon-format/)
- **.NET Implementation**: Available on NuGet

### **Learning Resources**

- **Benchmark Results**: [github.com/toon-format/toon#benchmarks](https://github.com/toon-format/toon#benchmarks)
- **Token Analysis Tools**: Built into the Python library
- **Interactive Playground**: Coming soon on toonformat.dev

### **Community & Support**

- **GitHub Issues**: [github.com/toon-format/toon/issues](https://github.com/toon-format/toon/issues)
- **Discussions**: [github.com/toon-format/toon/discussions](https://github.com/toon-format/toon/discussions)

---

## 📈 Benchmark Results & Performance

Independent testing across **4 major LLMs** on **209 data retrieval questions** showed:

| Metric | Accuracy |
|--------|----------|
| **Field Retrieval** | 99.6% |
| **Structure Awareness** | 88.0% |
| **Structural Validation** | 70.0% |
| **Overall** | 73.9% |

**Model-Specific Performance:**
- Claude Haiku: 59.8%
- Gemini 2.5: 87.6%
- GPT-5 Nano: 90.9%

**Token Savings:**
- Average: 30-60% reduction vs formatted JSON
- Best case (uniform arrays): Up to 67% reduction
- Minimal overhead vs CSV: Only 5-10%

---

## 🎯 Real-World Use Cases

### **1. RAG Systems**
When feeding large document chunks with metadata to LLMs, TOON can reduce prompt tokens by 40%, allowing you to fit more context within token limits.

### **2. Log Analysis**
Processing 100,000 lines of structured logs? TOON reduces token usage by over 20%, enabling more data per API call.

### **3. E-commerce Product Feeds**
Feeding product catalogs to LLMs for recommendations becomes significantly more cost-effective with TOON's tabular format.

### **4. Financial Data Processing**
Transaction histories, stock data, and financial records compress beautifully in TOON format while maintaining complete accuracy.

### **5. IoT Sensor Data**
Streaming sensor readings with timestamps, values, and metadata? TOON's uniform array handling makes this highly efficient.

---

## 🔧 Advanced Features

### **Nested Structures**
TOON handles nested objects with indentation:

```toon
company:
 name: TechCorp
 employees[2]{id,name,department}:
  1,Alice,Engineering
  2,Bob,Sales
 locations[2]{city,country}:
  San Francisco,USA
  London,UK
```

### **File Convention**
- Extension: `.toon`
- Media type: `text/toon` (provisional)
- Encoding: Always UTF-8

### **Schema Inference**
TOON's explicit headers `{field1,field2,field3}` help LLMs:
- Validate field existence
- Understand data structure
- Generate accurate queries
- Detect missing or malformed data

---

## 💡 Key Takeaways

1. **TOON reduces token usage by 30-60%** compared to JSON for structured data
2. **Accuracy improves** because explicit schemas help LLMs parse data better
3. **Easy to integrate** — simple encode/decode functions in your existing code
4. **Cost savings scale** — the more structured data you use, the more you save
5. **It's not a replacement for everything** — use it where uniform arrays shine

---

## 🚦 Getting Started Checklist

- [ ] Install TOON library for your language (`npm install @toon-format/toon` or `pip install toon-format`)
- [ ] Identify high-volume structured data in your prompts (arrays of objects)
- [ ] Replace JSON encoding with TOON encoding (`encode(data)`)
- [ ] Measure token savings with your actual data
- [ ] Monitor LLM response quality (should maintain or improve)
- [ ] Calculate cost savings over a week/month
- [ ] Scale to all relevant prompts in your application

---

## 🎓 Additional Considerations

### **When JSON Still Makes Sense**
- Single objects without arrays
- Deeply nested irregular structures
- Interoperability requirements (APIs, databases)
- Client-side JavaScript directly parsing responses

### **Performance Notes**
- **Encoding/Decoding**: Slightly slower than native JSON (negligible for most use cases)
- **LLM Processing**: Faster due to fewer tokens to process
- **Network**: Smaller payload sizes when sending data

### **Version Compatibility**
TOON is still evolving. Pin your library versions in production:
```json
{
  "dependencies": {
    "@toon-format/toon": "^1.0.0"
  }
}
```

---

## 📚 Further Reading

- [InfoQ Article: TOON Hopes to Cut LLM Costs](https://www.infoq.com/news/2025/11/toon-reduce-llm-cost-tokens/)
- [Medium: Introducing TOON for AI Workloads](https://medium.com/@nizzola.dev/introducing-toon-an-optimized-serialization-format-for-ai-and-llm-workloads-cbaacdd5167e)
- [FreeCodeCamp: What is TOON?](https://www.freecodecamp.org/news/what-is-toon-how-token-oriented-object-notation-could-change-how-ai-sees-data/)

---

## 🤝 Contributing to TOON

TOON is open-source (MIT License) and welcomes contributions:

- **Report bugs**: [GitHub Issues](https://github.com/toon-format/toon/issues)
- **Suggest features**: [GitHub Discussions](https://github.com/toon-format/toon/discussions)
- **Submit PRs**: Check the contributing guidelines in the repo
- **Add language support**: Implement TOON for your favorite language

---

## 📝 Summary

TOON represents a paradigm shift in how we feed data to LLMs. By optimizing specifically for token efficiency while maintaining human readability and improving model accuracy, it offers a compelling solution for cost-conscious AI applications.

**The bottom line**: If you're regularly feeding structured data to LLMs, TOON can save you 30-60% on tokens, which translates directly to lower API costs and faster processing times.

Start small, measure the impact, and scale up. Your LLM budget will thank you.

---

**Newsletter prepared on**: November 26, 2025
**Version**: 1.0
**License**: This newsletter is provided for educational purposes.

---

## 📧 Feedback & Questions

Have questions about implementing TOON? Found an interesting use case? Share your experience and help the community learn!

Happy optimizing! 🚀
