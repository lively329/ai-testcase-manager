# Test Case Management System

[中文版](README_zh.md)

A modern test case management system built with Flask and Vue 3, supporting Excel file batch upload, project management, test case viewing, and AI-powered intelligent generation functionalities.

### Recommended for use QWEN_MODEL = 'qwen-plus-latest'

## Project Features
### project img


### Core Functionality
- Project Management: Create, edit, and delete projects with descriptions and maintainer information
- Excel Batch Upload: Support for .xlsx format file uploads with intelligent Excel content parsing
- Duplicate Detection: Automatic detection of duplicates within files and in the database
- Smart Import: Selective import support with ability to assign new IDs to duplicate cases
- Test Case Viewing: View associated test cases by project with detailed case information display
- **AI Test Case Generation**: Intelligent test case generation based on documents, supporting multiple test types
- Responsive Interface: Modern UI design with sidebar navigation support

### Technical Features
- Separation of Frontend and Backend: Flask RESTful API with Vue 3 SPA
- Database Support: MySQL database with support for complex queries and associations
- File Processing: Excel file parsing and validation capabilities
- **AI Integration**: Integration with AutoGen framework and OpenAI models for intelligent test case generation
- **Document Processing**: Support for Word, PDF, Markdown and other document formats
- Error Handling: Comprehensive exception handling with user-friendly error messages
- Component Communication: Event-based inter-component communication mechanism

## Technology Stack

### Backend
- Python 3.8+
- Flask: Web framework
- Flask-CORS: Cross-origin support
- Flask-MySQLdb: MySQL database connection
- openpyxl: Excel file processing
- Werkzeug: File upload handling
- **AutoGen: AI agent framework**
- **OpenAI: AI model integration**
- **Browser-use: Web automation**
- **Playwright: Browser automation**
- **Document processing: python-docx, PyPDF2, markdown**

### Frontend
- Vue 3: Progressive JavaScript framework
- Vue Router: Routing management
- Element Plus: UI component library
- Axios: HTTP client
- Vite: Build tool

### Database
- MySQL 5.7+: Relational database

## Environment Requirements

### System Requirements
- Python 3.8 or higher
- Node.js 20.19+ or 22.12+
- MySQL 5.7 or higher
- Git
- **Supported Operating Systems**: Windows, macOS, Linux

### Python Dependencies
The project uses the following key Python packages:
- **Web Framework**: Flask, Flask-CORS, Flask-MySQLdb
- **AI & Automation**: autogen, openai, browser_use, playwright
- **Document Processing**: python-docx, PyPDF2, markdown, pandas, openpyxl
- **Development Tools**: pytest, black, isort, flake8
- **Utilities**: python-dotenv, pydantic, asyncio, tenacity

### Node.js Dependencies
The frontend uses the following key packages:
- **Framework**: Vue 3, Vue Router
- **UI Library**: Element Plus
- **Build Tool**: Vite
- **HTTP Client**: Axios

## Project Structure

```
JoinTestCase/
├── backend/                 # Backend code
│   ├── app.py              # Flask application main file
│   ├── models/             # Data models
│   │   ├── __init__.py
│   │   └── db.py          # Database connection
│   ├── routes/             # Routing modules
│   │   ├── __init__.py
│   │   ├── project.py     # Project management routes
│   │   ├── test_case.py   # Test case routes
│   │   ├── upload.py      # File upload routes
│   │   └── ai_generate.py # AI test case generation routes
│   ├── ai_test_cases/      # AI testing system
│   │   ├── src/           # AI system source code
│   │   │   ├── agents/    # AI agents
│   │   │   ├── services/  # AI services
│   │   │   ├── templates/ # Test case templates
│   │   │   └── utils/     # AI utilities
│   │   ├── docs/          # Document storage
│   │   └── requirements.txt # AI system dependencies
│   └── uploads/           # Uploaded file storage directory
├── frontend/              # Frontend code
│   ├── src/
│   │   ├── api/           # API interfaces
│   │   │   └── case.js    # Test case related APIs
│   │   ├── assets/        # Static resources
│   │   ├── components/    # Vue components
│   │   ├── views/         # Page components
│   │   │   ├── ManageCase.vue    # Project management page
│   │   │   ├── UploadCase.vue    # Test case upload page
│   │   │   └── AiGenerateCase.vue # AI test case generation page
│   │   ├── App.vue        # Root component
│   │   ├── main.js        # Application entry point
│   │   └── router.js      # Routing configuration
│   ├── index.html         # HTML template
│   ├── package.json       # Dependency configuration
│   └── vite.config.js     # Vite configuration
├── requirements.txt        # Python dependencies (root level)
├── config.py              # Database configuration
├── db_init.sql           # Database initialization script
├── read_excel.py         # Excel reading tool
└── 登录测试用例.xlsx      # Sample Excel file
```

## Database Design

### Core Table Structures

#### projects table (Project table)
```sql
CREATE TABLE projects (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    maintainers VARCHAR(255) DEFAULT ''
);
```

#### test_cases table (Test case table)
```sql
CREATE TABLE test_cases (
    id INT AUTO_INCREMENT PRIMARY KEY,
    case_id VARCHAR(200) NOT NULL,
    title VARCHAR(255),
    description TEXT,
    preconditions TEXT,
    steps TEXT,
    expected_results TEXT,
    priority VARCHAR(50),
    category VARCHAR(100),
    status VARCHAR(50),
    created_at DATETIME,
    updated_at DATETIME,
    created_by VARCHAR(100),
    last_updated_by VARCHAR(100),
    project_id INT,
    FOREIGN KEY (project_id) REFERENCES projects(id)
);

-- Add unique constraint within project to ensure case_id uniqueness within the same project
ALTER TABLE test_cases ADD UNIQUE INDEX unique_case_id_per_project (project_id, case_id);
```

#### project_cases table (Project-test case association table)
```sql
CREATE TABLE project_cases (
    id INT AUTO_INCREMENT PRIMARY KEY,
    project_id INT,
    test_case_id INT,
    FOREIGN KEY (project_id) REFERENCES projects(id),
    FOREIGN KEY (test_case_id) REFERENCES test_cases(id)
);
```

#### ai_test_generation_history table (AI test case generation history table)
```sql
CREATE TABLE `ai_test_generation_history` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `case_types` json DEFAULT NULL,
  `priority_distribution` json DEFAULT NULL,
  `total_cases` int(11) DEFAULT '0',
  `functional_test_count` int(11) DEFAULT '0',
  `api_test_count` int(11) DEFAULT '0',
  `ui_auto_test_count` int(11) DEFAULT '0',
  `estimated_file_size` bigint(20) DEFAULT NULL,
  `generated_at` datetime DEFAULT NULL,
  `filename` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `created_at` timestamp NULL DEFAULT CURRENT_TIMESTAMP,
  `updated_at` timestamp NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`),
  KEY `idx_generated_at` (`generated_at`),
  KEY `idx_filename` (`filename`)
) ENGINE=InnoDB AUTO_INCREMENT=9 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

## Quick Start

### Environment Requirements
- Python 3.8+
- Node.js 20.19.0+
- MySQL 5.7+

### 1. Clone the Project
```bash
git clone <repository-url>
cd JoinTestCase
```

### 2. Backend Setup

#### Install Python Dependencies
```bash
# Install all Python dependencies from requirements.txt
pip install -r requirements.txt

# Or install core dependencies manually
pip install flask flask-cors flask-mysqldb openpyxl werkzeug python-dotenv autogen openai asyncio pydantic fastapi uvicorn browser_use playwright python-docx PyPDF2 markdown pandas python-multipart aiofiles typing tenacity numpy matplotlib pytest pytest-asyncio pytest-cov black isort flake8
```

#### Database Configuration
1. Create MySQL database
```sql
CREATE DATABASE testcase_manager CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

2. Modify database configuration in `config.py`
```python
class Config:
    MYSQL_HOST = 'localhost'
    MYSQL_PORT = 3306
    MYSQL_USER = 'your_username'
    MYSQL_PASSWORD = 'your_password'
    MYSQL_DB = 'testcase_manager'
    MYSQL_CURSORCLASS = 'DictCursor'
```

3. Initialize database
```bash
mysql -u your_username -p testcase_manager < db_init.sql
```

#### Start Backend Service
```bash
cd backend
python app.py
```
Backend service will start at `http://localhost:5000`

### 3. Frontend Setup

#### Install Node.js Dependencies
```bash
cd frontend
npm install
```

### 4. AI Interface Configuration
1. Navigate to AI system directory
```bash
cd backend/ai_test_cases
```

2. Install AI system dependencies
```bash
pip install -r requirements.txt
```

3. Configure AI model parameters
- After starting the application, configure AI model parameters in the system settings page
- Supports multiple AI models including Tongyi Qianwen, Volcengine, and others
- Configuration includes API keys, model URLs, model versions, and other parameters
- All configurations are done through the web interface, no need to manually edit configuration files

#### Start Development Server
```bash
npm run dev
```
Frontend application will start at `http://localhost:5173`

## Usage Instructions

### Project Management
1. Create Project: Click the "Create Project" button and fill in project name, description, and maintainers
2. Edit Project: Click the "Edit" button in the project list to modify project information
3. Delete Project: Click the "Delete" button and confirm to delete the project (also deletes associated test cases)

### Test Case Upload
1. Select Project: Choose the project to import test cases into on the upload page
2. Upload File: Click the "Select File" button and choose an Excel file in .xlsx format
3. Preview Data: The system will automatically parse the Excel content and display a preview
4. Handle Duplicates: 
   - The system automatically detects duplicate case IDs
   - Duplicate cases will be highlighted with a red background
   - New IDs can be assigned to duplicate cases
5. Select Import: Check the cases to import and click "Import Selected Cases"

### Test Case Viewing
1. View Project Cases: Click the "View Cases" button on the project management page
2. View Case Details: Click the "View" button in the case list to see complete case information

### AI Model Configuration
1. **Access Configuration Page**: Configure AI model parameters in the system settings page
2. **Select Model Type**: Supports multiple mainstream AI models:
   - **Tongyi Qianwen** (Alibaba Cloud) - Recommended to use qwen-turbo or qwen-plus
   - **ByteDance (Volcengine)** - Supports skylark-chat-pro and other models
   - **DeepSeek** - Supports deepseek-chat and other models
   - **Zhipu AI** - Supports glm-4, glm-3-turbo and other models
   - **OpenAI** - Supports GPT-4, GPT-3.5-turbo and other models
   - **Baidu ERNIE Bot** - Supports ERNIE-Bot-4, ERNIE-Bot-turbo and other models
   - **Xunfei Spark** - Supports SparkDesk and other models
   - **MiniMax** - Supports abab5.5-chat and other models
   - **Moonshot** - Supports moonshot-v1-8k and other models
   - **360 AI** - Supports 360GPT-S2 and other models
   - **Claude (Anthropic)** - Supports Claude-3.5-Sonnet, Claude-3-Opus and other models
   - **Gemini (Google)** - Supports gemini-pro, gemini-pro-vision and other models
3. **Configure Parameters**: 
   - API Key: Enter the API key for the corresponding AI service provider
   - Model URL: Configure the model interface address (system provides default addresses)
   - Model Version: Select the specific model version
   - Price Settings: Configure input and output token prices
4. **Save Configuration**: Click the "Save Configuration" button to complete the setup
5. **Verify Configuration**: The system will automatically verify the validity of the configuration

### AI Test Case Generation
1. **Upload Documents**: Upload requirement documents in the AI generation page (supports Word, PDF, Markdown and other formats)
2. **Configure Parameters**: 
   - Select test type (Functional Testing, API Testing, UI Automation Testing)
   - Set concurrency (1-5)
   - Specify output filename
3. **Start Generation**: Click the "Start Generation" button, the system will automatically analyze documents and generate test cases
4. **Monitor Progress**: The system displays generation progress and status
5. **View Results**: After completion, view and download generated test case files in the download center

## API Interfaces

### Project Management
- `GET /project` - Get all projects
- `POST /project` - Create a new project
- `PUT /project/{id}` - Update a project
- `DELETE /project/{id}` - Delete a project
- `GET /project/{id}/testcases` - Get test cases associated with a project

### File Upload
- `POST /upload_case` - Upload Excel file and parse
- `POST /import_case` - Import selected test cases

### AI Model Configuration
- `GET /api/ai_config` - Get AI model configuration
- `POST /api/ai_config` - Save AI model configuration

### AI Test Case Generation
- `POST /ai_generate/upload` - Upload requirement documents
- `POST /ai_generate/generate` - Generate test cases
- `GET /ai_generate/files` - Get list of generated files
- `GET /ai_generate/download/{filename}` - Download generated test case files
- `GET /ai_generate/summary` - Get generation result summary
- `GET /ai_generate/latest_summary` - Get latest generation summary

## UI Features

### Design Style
- Modern UI: Modern interface design based on Element Plus
- Responsive Layout: Adapts to different screen sizes
- Sidebar Navigation: Clear navigation structure
- Card Layout: Well-organized information hierarchy

### Interaction Experience
- Smart Tables: Adaptive column widths with sorting and filtering support
- Modal Operations: Modal dialog-based create, update, and delete operations
- Real-time Feedback: Immediate feedback on operations
- Error Messages: User-friendly error information display
- Progress Display: Real-time progress display for AI generation process

## Function Details

### Excel File Processing
- Format Support: Supports only .xlsx format
- Column Mapping: Automatically identifies Excel headers and maps to database fields
- Data Cleaning: Automatically handles null values and special characters
- Duplicate Detection: Intelligent detection of duplicates within files and in the database

### AI Test Case Generation
- **Document Parsing**: Intelligent parsing support for multiple document formats
- **Test Case Types**: Support for three types: Functional Testing, API Testing, UI Automation Testing
- **Intelligent Analysis**: AI model-based requirement analysis and test case design
- **Batch Generation**: Support for batch generation of large numbers of test cases
- **Quality Assurance**: Generated test cases include complete test steps and expected results

### Data Validation
- File Format Validation: Ensures uploaded files are valid format
- Data Integrity: Validates the presence of required fields
- ID Uniqueness: Ensures test case ID uniqueness

### Error Handling
- File Upload Errors: Handles file format errors and upload failures
- Database Errors: Handles database connection and operation exceptions
- Network Errors: Handles API request timeouts and network exceptions
- AI Generation Errors: Handles AI model call failures and generation exceptions

## Troubleshooting

### Common Issues

#### 1. AI Generation Failure
**Problem**: Errors occur during AI test case generation process
**Solutions**:
- Check if AI interface configuration is correct
- Confirm if API key is valid
- Check network connection stability
- View backend logs for detailed error information

#### 2. Database Connection Failure
**Problem**: Unable to connect to MySQL database
**Solutions**:
- Check if database service is running
- Confirm database connection parameters are correct
- Check firewall settings
- Verify database user permissions

#### 3. Frontend Proxy Errors
**Problem**: Proxy errors such as ECONNRESET occur
**Solutions**:
- Restart frontend and backend services
- Check if ports are occupied
- Confirm Vite proxy configuration is correct
- Check network connection stability

#### 4. File Upload Failure
**Problem**: Errors occur during file upload process
**Solutions**:
- Check if file format is supported
- Confirm file size is within limits
- Check upload directory permissions
- Verify file content integrity

### Log Viewing
- **Backend Logs**: View error information output in console
- **Frontend Logs**: View error information in browser console
- **Database Logs**: View MySQL error logs

## Deployment Instructions

### Production Deployment
1. Backend Deployment: Deploy Flask application using Gunicorn or uWSGI
2. Frontend Build: Run `npm run build` to build production version
3. Static File Serving: Use Nginx to serve frontend static files
4. Database Optimization: Configure MySQL performance parameters and indexes

### Environment Variable Configuration
```bash
# Database configuration
MYSQL_HOST=localhost
MYSQL_PORT=3306
MYSQL_USER=your_username
MYSQL_PASSWORD=your_password
MYSQL_DB=testcase_manager

# Flask configuration
FLASK_ENV=production
FLASK_SECRET_KEY=your_secret_key

# AI interface configuration
QWEN_BASE_URL=https://your-ai-api-endpoint.com/v1
QWEN_API_KEY=your-api-key
QWEN_MODEL=qwen-turbo
```

### Performance Optimization
1. **Database Optimization**: Add appropriate indexes, optimize query statements
2. **Caching Strategy**: Use Redis to cache frequently accessed data
3. **Load Balancing**: Use Nginx for load balancing
4. **Monitoring and Alerting**: Configure system monitoring and alerting mechanisms

## Contribution Guidelines

1. Fork the project
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request