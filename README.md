# Large Language Model Gateway (LLMGW)

![License](https://img.shields.io/badge/license-MIT-green)
![Version](https://img.shields.io/badge/version-1.0-blue)

## Overview

The **Large Language Model Gateway (LLMGW)** is a API middleware designed to interface with AI models for chat completions. This API provides endpoints to interact with AI models, manage prompts, and retrieve completions, with built-in rate limiting, security, and clustering for scalability.

## Features

- **AI Model Integration**: Send prompts to AI models and get responses.
- **Scalable Architecture**: Utilizes clustering to fork workers across CPU cores for better performance.
- **Rate Limiting**: Built-in rate limiting to prevent abuse (1000 requests per 15 minutes).
- **Security**: Uses [Helmet](https://github.com/helmetjs/helmet) for secure HTTP headers.
- **CLI Interaction**: Support for clearing the console and exiting the server via command line.
- **Verbose Logging**: Optional detailed logging of requests and responses.

### Command-line options

| Option          | Description                                | Default               |
|-----------------|--------------------------------------------|-----------------------|
| `--bindip`      | IP address to bind the server to           | `127.0.0.1`             |
| `--bindport`    | Port to bind the server to                 | `42069`                |
| `--aihost`      | AI model server host                       | `10.0.0.1`      |
| `--aihostport`  | AI model server port                       | `443`               |
| `--verbose`     | Enable verbose logging                     | `false`               |

### Example:

To start the server on IP `127.0.0.1`, port `5000`, and connect to AI model at `localhost:8000` with verbose logging:

```bash
./llmw --bindip 127.0.0.1 --bindport 42069 --aihost 10.0.0.1 --aihostport 443 --verbose
```

## API Documentation

### POST `/v1/chat/completions`

#### Request

- **Headers**: `Content-Type: application/json`
- **Body**:
  ```json
  {
    "model": "string",             // (Optional) Model ID, default: "TheBloke/Mistral-7B-Instruct-v0.2-AWQ"
    "messages": [
      {
        "role": "string",           // (Required) Role of the message (user/system/assistant)
        "content": "string"         // (Required) Content of the message
      }
    ],
    "max_tokens": 128,              // (Optional) Maximum number of tokens in the response
    "temperature": 0.7              // (Optional) Sampling temperature (0.1 - 1.0)
  }
  ```

#### Response

- **Status 200**:
  ```json
  {
    "id": "string",                          // Unique ID for the completion
    "object": "chat.completion",             // Response type
    "created": 1636107200,                   // Timestamp (Unix epoch)
    "model": "string",                       // Model ID used
    "choices": [
      {
        "message": {
          "role": "user",
          "content": "string"                // AI response content
        },
        "finish_reason": "stop",
        "index": 0
      }
    ],
    "usage": {
      "prompt_tokens": 123,
      "completion_tokens": 45,
      "total_tokens": 168
    }
  }
  ```

- **Error (400 Bad Request)**:
  ```json
  {
    "error": {
      "message": "Temperature must be between 0.1 and 1.0",
      "type": "invalid_request_error"
    }
  }
  ```

## Development

1. **Testing the API Locally**: You can use tools like [Postman](https://www.postman.com/) or [curl](https://curl.se/) to test the API.
   
   Example request using `curl`:
   ```bash
   curl -X POST http://localhost:42069/v1/chat/completions \
   -H "Content-Type: application/json" \
   -d '{
     "model": "TheBloke/Mistral-7B-Instruct-v0.2-AWQ",
     "messages": [{"role": "user", "content": "Hello AI"}],
     "max_tokens": 100,
     "temperature": 0.7
   }'
   ```

2. **Logging**: If the `--verbose` flag is enabled, detailed logs will appear in the console, showing received inputs and AI responses with ANSI color coding.

## Scalability

The server uses **clustering** to distribute requests across multiple CPU cores. By default, it forks workers equal to the number of available CPU cores.

If you need to change this behavior, you can modify the clustering logic in the code.

## Security

This API uses **Helmet** for security by adding HTTP headers that prevent some common attacks such as cross-site scripting (XSS) and clickjacking. It also implements rate-limiting to prevent abuse.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Contributing

Contributions are welcome! Please feel free to submit a pull request or create an issue if you encounter any problems.

---

This `README.md` covers installation, configuration, usage, and development guidelines for users and contributors. It is designed to be placed on a GitHub repository to assist developers in deploying and interacting with the API.
