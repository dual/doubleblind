# DoubleBlind

> **Intelligent PII Detection and Replacement for Python**

[![Python](https://img.shields.io/badge/python-3.9%2B-blue)](https://pypi.org/project/doubleblind/)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![CircleCI](https://circleci.com/gh/dual/doubleblind.svg?style=shield)](https://circleci.com/gh/dual/doubleblind)
[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=dual_doubleblind&metric=alert_status)](https://sonarcloud.io/summary/new_code?id=dual_doubleblind)
[![Coverage](https://sonarcloud.io/api/project_badges/measure?project=dual_doubleblind&metric=coverage)](https://sonarcloud.io/summary/new_code?id=dual_doubleblind)
[![Bugs](https://sonarcloud.io/api/project_badges/measure?project=dual_doubleblind&metric=bugs)](https://sonarcloud.io/summary/new_code?id=dual_doubleblind)
[![pypi package](https://img.shields.io/pypi/v/doubleblind-api?color=%2334D058&label=pypi%20package)](https://pypi.org/project/doubleblind/)
[![python](https://img.shields.io/pypi/pyversions/doubleblind-api.svg?color=%2334D058)](https://pypi.org/project/doubleblind)
[![contributions welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)](https://github.com/dual/doubleblind/issues)

DoubleBlind is a powerful, flexible library for automatically detecting and replacing Personally Identifiable Information (PII) in your data structures. Whether you're anonymizing datasets for testing, protecting sensitive information in logs, or ensuring GDPR compliance, DoubleBlind makes it effortless.

## ✨ Key Features

- **🚀 High Performance**: Choose between fast JSON-based processing or flexible tree traversal
- **🎯 Smart Detection**: Built-in patterns for emails, phones, SSNs, credit cards, IPs, and URLs
- **🔧 Highly Configurable**: Custom patterns, callbacks, and replacement strategies
- **📊 Realistic Fake Data**: Generate believable replacements using the Faker library
- **🌳 Deep Traversal**: Handle complex nested data structures automatically
- **⚡ Zero Dependencies**: Lightweight with minimal external requirements
- **🛡️ Type Safe**: Full TypeScript-style type hints for better development experience
- **📋 Path Targeting**: Precisely target specific data paths for replacement

## 🎯 Why DoubleBlind?

**The Problem**: You have sensitive data in complex structures that needs to be anonymized for testing, logging, or compliance, but existing solutions are either too rigid, too slow, or don't handle your specific use cases.

**The Solution**: DoubleBlind provides intelligent PII detection with multiple processing strategies, letting you choose the perfect balance of performance and flexibility for your needs.

## 🚀 Quick Start

### Installation

```bash
pip install doubleblind
# or
pipenv install doubleblind
# or
poetry add doubleblind
```

### Basic Usage

```python
from doubleblind import DoubleBlind

# Initialize with default settings
db = DoubleBlind()

# Your data with PII
data = [
    {
        "user_id": 12345,
        "name": "John Doe",
        "email": "john.doe@company.com",
        "phone": "555-123-4567",
        "ssn": "123-45-6789"
    },
    {
        "customer": {
            "contact": "jane@example.org",
            "billing": {
                "card": "4532-1234-5678-9012",
                "address": "123 Main St"
            }
        }
    }
]

# Replace PII automatically
masked_data = db.mask_data(data)

print(masked_data)
# Output:
# [
#   {
#     "user_id": 12345,
#     "name": "John Doe", 
#     "email": "****@******.***",
#     "phone": "***-***-****",
#     "ssn": "***-**-****"
#   },
#   ...
# ]
```

## 🔧 Advanced Configuration

### Using Realistic Fake Data

```python
from doubleblind import DoubleBlind

# Generate realistic fake data instead of asterisks
db = DoubleBlind(use_faker=True)

masked_data = db.mask_data(data)
# Emails become: sarah.johnson@example.net
# Phones become: +1-555-234-5678  
# SSNs become: 987-65-4321
```

### Custom Replacement Logic

```python
def custom_replacer(item, key, pattern_type, breadcrumbs):
    """Custom replacement with full context"""
    if pattern_type == 'email':
        return "***REDACTED_EMAIL***"
    elif pattern_type == 'ssn':
        return "XXX-XX-XXXX"
    else:
        return "***CLASSIFIED***"

db = DoubleBlind(callback=custom_replacer)
```

### Targeting Specific Patterns

```python
# Only replace certain types, allow others through
db = DoubleBlind(
    allowed=['email'],  # Don't replace emails
    extras=[r'CUST-\d+', r'REF-[A-Z]{3}-\d{4}']  # Custom patterns
)
```

### Precise Path Targeting

```python
# Only replace PII at specific data paths
db = DoubleBlind(
    known_paths=[
        'customer.email',
        'billing.ssn', 
        'contacts.emergency.phone'
    ]
)
```

## 🏗️ Architecture

DoubleBlind offers two complementary processing strategies:

### 🚀 JSONGrepper (High Performance)

- **Best for**: Large datasets, simple replacement needs
- **Speed**: ⚡ Fastest option
- **Method**: JSON serialization + regex replacement
- **Trade-offs**: Less flexibility, no custom callbacks

```python
# Automatically chosen when no custom logic needed
db = DoubleBlind()  # Uses JSONGrepper internally
```

### 🌳 DictWalker (Maximum Flexibility)

- **Best for**: Complex logic, custom callbacks, path targeting
- **Speed**: 🐢 Slower but more capable  
- **Method**: Recursive tree traversal
- **Features**: Full context, breadcrumbs, custom callbacks

```python
# Automatically chosen when using advanced features
db = DoubleBlind(use_faker=True)  # Uses DictWalker
db = DoubleBlind(callback=my_func)  # Uses DictWalker
```

## 📊 Built-in PII Patterns

| Pattern | Description | Example |
|---------|-------------|---------|
| `email` | Email addresses | `user@domain.com` |
| `phone` | Phone numbers (US formats) | `555-123-4567`, `(555) 123-4567` |
| `ssn` | Social Security Numbers | `123-45-6789`, `123456789` |
| `credit_card` | Credit card numbers | `4532-1234-5678-9012` |
| `ip_address` | IPv4 addresses | `192.168.1.1` |
| `url` | HTTP/HTTPS URLs | `https://example.com/path` |

## 🎛️ Configuration Options

```python
db = DoubleBlind(
    use_faker=False,           # Use fake data vs asterisks
    callback=None,             # Custom replacement function
    allowed=[],                # Pattern types to skip
    extras=[],                 # Additional regex patterns  
    known_paths=[],            # Specific paths to target
    replace_with='*',          # Character for replacements
    maintain_length=False      # Preserve original string length
)
```

## 🧪 Real-World Examples

### API Response Sanitization

```python
# Sanitize API responses for logging
api_response = {
    "status": "success",
    "data": {
        "users": [
            {"id": 1, "email": "user1@corp.com", "role": "admin"},
            {"id": 2, "email": "user2@corp.com", "role": "user"}
        ]
    },
    "metadata": {"request_ip": "203.0.113.42"}
}

db = DoubleBlind()
safe_response = db.mask_data([api_response])[0]
# Safe to log without exposing PII
```

### Database Export Anonymization

```python
# Anonymize database exports for development
db_records = [
    {"patient_id": "PT001", "ssn": "123-45-6789", "email": "patient@email.com"},
    {"patient_id": "PT002", "ssn": "987-65-4321", "email": "another@email.com"}
]

db = DoubleBlind(
    use_faker=True,
    allowed=[],  # Replace all PII types
)

anonymized_records = db.mask_data(db_records)
# Safe for development environments
```

### Configuration File Sanitization

```python
# Remove secrets from config files
config = {
    "database": {
        "host": "db.company.com",
        "admin_email": "admin@company.com"
    },
    "api_keys": {
        "stripe": "sk_live_abcd1234...",
        "support_email": "support@company.com"
    }
}

db = DoubleBlind(known_paths=['database.admin_email', 'api_keys.support_email'])
sanitized_config = db.mask_data([config])[0]
```

## 🔬 Performance & Testing

DoubleBlind includes comprehensive tests with 100% coverage:

```bash
# Run tests
pipenv run test

# Run with coverage
pipenv run pytest --cov=doubleblind tests/
```

**Performance Benchmarks** (10,000 records):

- JSONGrepper: ~0.1s (simple patterns)
- DictWalker: ~0.3s (with fake data generation)

## 🤝 Contributing

We welcome contributions! Please see our [contributing guidelines](CONTRIBUTING.md) for details.

```bash
# Development setup
git clone https://github.com/paulcruse3/doubleblind.git
cd doubleblind
pipenv install --dev
pipenv run test
```

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details.

## 🔗 Links

- [Documentation](https://github.com/paulcruse3/doubleblind/wiki) (coming soon)
- [Issues](https://github.com/paulcruse3/doubleblind/issues)
- [Changelog](CHANGELOG.md)
- [Security Policy](SECURITY.md)

---

> Made with ❤️ for data privacy and security
