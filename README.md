
# SonarQube Issues Export

This Python script fetches issues from a SonarQube project and exports them to CSV or Excel format. It uses the SonarQube REST API and handles pagination, date ranges, and chunked writing to efficiently retrieve and export large numbers of issues.

**Compatible with both local SonarQube instances (localhost:9000) and SonarCloud.**

## Prerequisites

- Python 3.x
- `requests`, `pandas`, `openpyxl` library
- Access to a SonarQube instance with an appropriate token

## Installation

1. Clone the repository:

```bash
git clone https://github.com/talha2k/sonarqube-issues-export-to-excel.git
cd sonarqube-issues-export-to-excel
```

2. Install the required Python libraries:

```bash
pip install requests pandas openpyxl
```

## Configuration

Configure the script using environment variables. The script works with both local SonarQube instances and SonarCloud.

Priority order used by the script:

1. OS environment variables
2. Values from `.env` file in the project root
3. Built-in default for `SONAR_URL` (`http://localhost:9000/api/issues/search`)

### For Local SonarQube Instance (default)

```bash
export SONAR_URL='http://localhost:9000/api/issues/search'   # Local SonarQube instance
export SONAR_PROJECT_KEY='your-project-key'                  # Your project key
export SONAR_TOKEN='your-authentication-token'               # Your authentication token
```

### For SonarCloud

```bash
export SONAR_URL='https://sonarcloud.io/api/issues/search'   # SonarCloud instance
export SONAR_PROJECT_KEY='your-project-key'                  # Your project key
export SONAR_TOKEN='your-authentication-token'               # Your authentication token
```

Alternatively, you can edit these values directly in the script.

### Using a `.env` file

Create a `.env` file in the repository root:

```env
SONAR_URL=http://localhost:9000/api/issues/search
SONAR_PROJECT_KEY=your-project-key
SONAR_TOKEN=your-authentication-token
```

Then run:

```bash
python sonar-export.py
```

PowerShell one-liner to create it quickly:

```powershell
@"
SONAR_URL=http://localhost:9000/api/issues/search
SONAR_PROJECT_KEY=your-project-key
SONAR_TOKEN=your-authentication-token
"@ | Set-Content .env
```

## Usage

### Basic Usage (Excel format)

```bash
python sonar-export.py
```

This will export issues to `sonarqube_issues.xlsx` by default.

### Export to CSV

For better cross-platform compatibility, you can export to CSV format:

```bash
python sonar-export.py --format csv
```

This will export issues to `sonarqube_issues.csv`.

### Export Options

```bash
python sonar-export.py --format [csv|xlsx]
```

- `--format csv`: Export to CSV format (better cross-platform compatibility, smaller file size)
- `--format xlsx`: Export to Excel format (default, better for viewing in spreadsheet applications)

### Date Range Examples

Use an explicit date range:

```bash
python sonar-export.py --start-date 2026-01-01 --end-date 2026-03-31
```

Use a rolling window ending today (default is 90 days):

```bash
python sonar-export.py --window-days 30
```

Combine format + date options:

```bash
python sonar-export.py --format csv --start-date 2026-01-01 --end-date 2026-02-01
```

## Features

- **Multiple Export Formats**: Export to CSV or Excel (XLSX) format
- **Chunked Writing**: Writes data in chunks (every 5000 issues) to minimize memory usage for large exports
- **Date Range Handling**: Automatically splits requests into date ranges to handle SonarQube's 10,000 result limit
- **Pagination Support**: Handles pagination to fetch all issues within each date range
- **Comprehensive Error Handling**: Includes specific error messages for common issues:
  - Authentication failures (401)
  - Project not found (404)
  - Access denied (403)
  - Connection timeouts
  - Network errors
- **Environment Variable Support**: Configure via environment variables for better security
- **Progress Reporting**: Shows real-time progress during export

## Example

Complete workflow example:

### Using Local SonarQube Instance

```bash
# Set up environment variables for local instance
export SONAR_URL='http://localhost:9000/api/issues/search'
export SONAR_PROJECT_KEY='my-project'
export SONAR_TOKEN='your-token-here'

# Export to Excel (default)
python sonar-export.py

# Export to CSV for cross-platform compatibility
python sonar-export.py --format csv
```

### Using SonarCloud

```bash
# Set up environment variables for SonarCloud
export SONAR_URL='https://sonarcloud.io/api/issues/search'
export SONAR_PROJECT_KEY='my-project'
export SONAR_TOKEN='your-token-here'

# Export to Excel (default)
python sonar-export.py

# Export to CSV for cross-platform compatibility
python sonar-export.py --format csv
```

Example output:
```
Fetching issues from 2025-01-01 to 2025-01-31...
Found 1234 issues so far...
Fetching issues from 2025-01-31 to 2025-03-02...
Found 2567 issues so far...
Writing chunk of 5000 issues to CSV...
...
✅ Export completed: 7891 issues exported to sonarqube_issues.csv
📊 Date range: 2025-01-01 to 2025-11-13
```

## Customization

You can customize export behavior using CLI arguments:

- `--start-date`: Set start date (`YYYY-MM-DD`)
- `--end-date`: Set end date (`YYYY-MM-DD`, default: current date)
- `--window-days`: Lookback window used when `--start-date` is not provided (default: 90)
- `--format`: Output file format (`csv` or `xlsx`)
- `delta`: Adjust the date range chunk size (default: 30 days)
- `chunk_size`: Change how often data is written to disk (default: 5000 issues)

## License

This project is licensed under the MIT License.
