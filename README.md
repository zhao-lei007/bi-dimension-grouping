# BI Dimension Grouping System

An intelligent dimension grouping system for BI platforms with rule-based auto-categorization capabilities.

## Overview

This project provides an automated solution for managing dimension value grouping in BI data platforms. It addresses the challenge of manually categorizing large volumes of dimension values (channels, regions, products, etc.) by implementing a rule-based engine that automatically groups values based on configurable patterns.

## Key Features

### 🎯 Smart Auto-Grouping
- **Pattern Matching**: Support for wildcard patterns (`*`, `%`, `?`) to match dimension values
- **Keyword Combinations**: AND/OR/NOT logic for complex matching rules
- **Priority System**: Configurable group priorities to handle conflicts when values match multiple rules

### 🔧 Flexible Rule Management
- **Visual Rule Editor**: Intuitive interface for creating and managing grouping rules
- **Real-time Preview**: See matching results instantly as you configure rules
- **Bulk Operations**: Support for batch rule creation and management

### 🧪 Testing & Validation
- **Rule Testing Interface**: Test rules against real or sample data before applying
- **Conflict Detection**: Automatically identify values that match multiple groups
- **Syntax Validation**: Built-in rule syntax checking with error hints

### 📊 Hybrid Grouping Strategy
- **Manual Override**: Maintain existing manual groupings with highest priority
- **Auto-categorization**: Automatically group new dimension values based on rules
- **Fallback Options**: Configure "Other" group for unmatched values

## Use Cases

### Channel Management
```
Rule: 头条*(效果) → "头条效果投放组"
Matches:
- 今日头条(效果) ✓
- 头条直播(效果) ✓
- 头条原生(效果) ✓
```

### Regional Grouping
```
Rule: *省 → "省级地区"
Rule: 北京*|上海*|广州*|深圳* → "一线城市"
```

### Product Categories
```
Rule: *运动* → "运动用品类"
Rule: %数码% AND NOT *测试* → "数码产品类"
```

## Technical Features

- **Pure Web Implementation**: No backend required, all logic runs in browser
- **Local Storage**: Business data persisted in browser's localStorage
- **Responsive Design**: Works on desktop and mobile devices
- **Export/Import**: Support for rule configuration backup and sharing

## Getting Started

### Prerequisites
- Modern web browser (Chrome, Firefox, Safari, Edge)
- Node.js 16+ (for development)

### Installation

1. Clone the repository:
```bash
git clone https://github.com/zhao-lei007/bi-dimension-grouping.git
cd bi-dimension-grouping
```

2. Install dependencies:
```bash
npm install
```

3. Start development server:
```bash
npm run dev
```

4. Open your browser and navigate to `http://localhost:3000`

### Production Deployment

Build the project:
```bash
npm run build
```

The built files will be in the `dist` directory, ready for deployment to any static hosting service.

## Project Structure

```
src/
├── components/       # UI components
│   ├── RuleEditor/   # Rule creation and editing
│   ├── TestPanel/    # Rule testing interface
│   └── GroupView/    # Grouping results display
├── services/         # Core business logic
│   ├── ruleEngine.ts # Rule matching engine
│   └── storage.ts    # Local storage management
├── utils/            # Utility functions
└── types/            # TypeScript definitions
```

## Rule Syntax Reference

| Pattern | Description | Example |
|---------|-------------|---------|
| `*` | Match any characters | `头条*` → 头条直播, 头条原生 |
| `%` | SQL-style wildcard | `%效果%` → 某某效果, 效果投放 |
| `?` | Match single character | `头条?` → 头条A, 头条1 |
| `AND` | Must contain all keywords | `头条 AND 效果` |
| `OR` | Contains any keyword | `抖音 OR 快手` |
| `NOT` | Exclude keyword | `直播 NOT 测试` |

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- Designed for BI teams dealing with high-volume dimension management
- Inspired by real-world challenges in marketing channel categorization
- Built with modern web technologies for maximum compatibility