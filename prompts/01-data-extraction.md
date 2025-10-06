# Data Extraction & Structuring Prompt

## 📋 Overview

**Use Case:** Extract structured data from unstructured text documents  
**Industry Application:** Converting emails, reports, invoices, or forms into structured databases  
**Difficulty Level:** Intermediate  
**Technique:** Few-shot learning with explicit output formatting

---

## 🎯 Objective

Transform unstructured text containing business information into clean, structured JSON or CSV format for database entry or analysis.

**Success Criteria:**
- 95%+ accuracy in field extraction
- Consistent formatting across outputs
- Handles missing data gracefully
- Identifies ambiguous cases

---

## 📝 The Prompt

### Version 3.0 (Current Best)

```
You are a data extraction specialist. Your task is to extract specific information from unstructured text and output it in a structured JSON format.

EXTRACTION RULES:
1. Extract only the requested fields
2. Use "null" for missing information
3. Standardize formats (dates as YYYY-MM-DD, phone as XXX-XXX-XXXX)
4. Flag uncertain extractions with "confidence": "low"
5. Preserve exact spelling of names and addresses

REQUIRED FIELDS:
- customer_name
- email
- phone
- order_date
- product_name
- quantity
- total_amount

OUTPUT FORMAT:
{
  "customer_name": "string",
  "email": "string",
  "phone": "string",
  "order_date": "YYYY-MM-DD",
  "product_name": "string",
  "quantity": number,
  "total_amount": number,
  "confidence": "high/medium/low",
  "notes": "any ambiguities or issues"
}

EXAMPLES:

Input: "Hey! I'm John Smith (john.smith@email.com) and I'd like to order 5 bags of organic fertilizer. My number is 509-555-0123. Can you get this to me by March 15th, 2024? Total was $247.50"

Output:
{
  "customer_name": "John Smith",
  "email": "john.smith@email.com",
  "phone": "509-555-0123",
  "order_date": "2024-03-15",
  "product_name": "Organic Fertilizer",
  "quantity": 5,
  "total_amount": 247.50,
  "confidence": "high",
  "notes": "Bags assumed as unit"
}

Input: "Customer Jane contacted us yesterday about the premium soil mix. Contact: jane.d@garden.com. She wants 10 units. Mentioned budget of around $300."

Output:
{
  "customer_name": "Jane D",
  "email": "jane.d@garden.com",
  "phone": null,
  "order_date": null,
  "product_name": "Premium Soil Mix",
  "quantity": 10,
  "total_amount": 300,
  "confidence": "medium",
  "notes": "Last name incomplete; amount is approximate ('around $300'); order date not specified"
}

Now extract data from this text:
[INSERT YOUR TEXT HERE]
```

---

## 🔄 Prompt Evolution

### Version 1.0 (Initial)
- Simple "extract the following fields" approach
- **Issue:** Inconsistent formatting, didn't handle missing data well
- **Accuracy:** ~70%

### Version 2.0 (Improved)
- Added explicit formatting rules
- Single example provided
- **Issue:** Still inconsistent with edge cases
- **Accuracy:** ~85%

### Version 3.0 (Current)
- Multiple examples showing different scenarios
- Confidence scoring added
- Clear rules for ambiguous cases
- Standardized output format
- **Accuracy:** ~95%

---

## 📊 Testing Results

### Test Scenarios (n=25)

| Scenario Type | Accuracy | Notes |
|---------------|----------|-------|
| Complete information | 100% | Perfect extraction |
| Missing 1-2 fields | 96% | Correctly marked as null |
| Ambiguous dates | 92% | Usually inferred correctly |
| Name variations | 88% | Struggles with initials only |
| Format variations | 94% | Good standardization |

### Common Edge Cases Tested

✅ **Handles Well:**
- Multiple date formats (MM/DD/YYYY, Month DD, YYYY, etc.)
- Informal language and abbreviations
- Missing optional fields
- Phone numbers with/without formatting

⚠️ **Needs Attention:**
- Names with only initials (confidence scoring helps)
- Ambiguous product names
- Implied vs explicit information

---

## 💡 Key Learnings

### What Works

1. **Multiple Examples Beat Long Instructions**
   - 2-3 diverse examples > lengthy rule explanations
   - Show edge cases in examples

2. **Confidence Scoring is Critical**
   - Allows model to flag uncertain extractions
   - User can review low-confidence items
   - Builds trust in the system

3. **Explicit Format Specifications**
   - JSON schema format prevents variation
   - Standardization rules reduce cleanup work

4. **Notes Field Adds Value**
   - Captures context that might be lost
   - Helps with quality control
   - Useful for edge case analysis

### What Doesn't Work

❌ Asking for "best guess" on missing data (leads to hallucinations)  
❌ Too many fields at once (>10 reduces accuracy)  
❌ Vague formatting instructions  
❌ No examples (too much room for interpretation)

---

## 🔧 Variations & Adaptations

### For Different Data Types

**Invoice Extraction:**
```
REQUIRED FIELDS:
- invoice_number
- vendor_name
- invoice_date
- due_date
- line_items (array)
- subtotal
- tax
- total
```

**Contact Information:**
```
REQUIRED FIELDS:
- full_name
- company
- title
- email
- phone
- linkedin_url
```

**Meeting Notes:**
```
REQUIRED FIELDS:
- date
- attendees (array)
- action_items (array)
- decisions_made (array)
- next_meeting_date
```

---

## 🎯 Use Case Examples

### Real-World Applications

1. **E-commerce Order Processing**
   - Extract order details from customer emails
   - Populate order management system
   - Reduce manual data entry by 80%

2. **Lead Generation**
   - Parse contact forms and inquiry emails
   - Structure data for CRM import
   - Automatic lead scoring based on completeness

3. **Document Digitization**
   - Convert scanned forms to database entries
   - Legacy document processing
   - Compliance record keeping

4. **Customer Support**
   - Extract key information from support tickets
   - Auto-categorization and routing
   - Faster response times

---

## 📈 Performance Metrics

**Time Savings:**
- Manual extraction: ~5 minutes per document
- AI extraction: ~10 seconds per document
- **96% time reduction**

**Accuracy Comparison:**
- Manual entry error rate: ~8%
- AI extraction error rate: ~5%
- **38% fewer errors** (with human review of low-confidence items)

**Cost Analysis:**
- Manual processing: $15/hour at 12 docs/hour = $1.25/doc
- AI processing: ~$0.02/doc (API costs)
- **98% cost reduction**

---

## 🚀 Implementation Tips

### Best Practices

1. **Start with a small test batch** (10-20 documents)
2. **Review low-confidence extractions** manually
3. **Build a feedback loop** - add problematic cases as examples
4. **Monitor accuracy** over time
5. **Update prompt** when patterns emerge

### Integration Approach

```python
# Pseudo-code for implementation
def extract_data(text):
    prompt = load_prompt_template()
    response = llm.generate(prompt + text)
    data = parse_json(response)
    
    if data['confidence'] == 'low':
        flag_for_review(data)
    else:
        insert_to_database(data)
    
    return data
```

---

## 🔍 Advanced Techniques

### Multi-Step Extraction

For complex documents, break into stages:

1. **Identify document type** (invoice, email, form)
2. **Extract with type-specific prompt**
3. **Validate extracted data**
4. **Cross-reference fields** for consistency

### Ensemble Approach

Run extraction with 2-3 different prompts and compare:
- If all agree: high confidence
- If majority agree: medium confidence
- If all disagree: flag for manual review

---

## 📚 Related Resources

- [Best Practices Guide](../resources/best-practices.md)
- [Prompt Templates](../resources/prompt-templates.md)
- [Testing Framework](../methodology/testing-framework.md)

---

## 📝 Version History

- **v3.0** (Oct 2025) - Added confidence scoring and notes field
- **v2.0** (Sep 2025) - Improved formatting rules and added example
- **v1.0** (Aug 2025) - Initial version

---

## 💬 Feedback & Questions

Have you used this prompt? Found improvements? Encountered edge cases?

Feel free to reach out: ggarrod76@gmail.com

---

**Last Updated:** October 2025  
**Tested On:** Claude 3.5 Sonnet, GPT-4, GPT-4 Turbo  
**Success Rate:** 95% (n=25 test documents)
