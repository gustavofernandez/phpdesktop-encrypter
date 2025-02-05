# PHPDesktop Encrypter

A simplified open-source tool for encrypting and obfuscating PHP files for use with PHPDesktop. This project protects your PHP source code by encrypting and (optionally) obfuscating both pages and components, then generates a router file that decrypts and executes them at runtime.

> **Note:** This version uses MSYS2 for compilation and expects the use of OpenSSL for encryption.

## Table of Contents

- [Features](#features)
- [Requirements](#requirements)
- [Folder Structure](#folder-structure)
- [Usage](#usage)
- [Compiling the Encrypter](#compiling-the-encrypter)
- [Integrating with PHPDesktop](#integrating-with-phpdesktop)
- [PHPDesktop Settings](#phpdesktop-settings)
- [License](#license)

## Features

- **Encryption/Obfuscation:** Encrypts all PHP files (pages and components) and generates a JSON map for decryption.
- **Router Generation:** Produces a router file (`router.php`) that, in production mode, decrypts and evaluates the encrypted files.
- **Dual Mode:**  
- **Development Mode:** Loads PHP files directly for ease of testing.
- **Production Mode:** Loads encrypted/obfuscated files via decryption at runtime.
- **Open Source & Simplified:** Designed to be a starting point for anyone wanting to protect their PHP source in PHPDesktop.
- **System:** Windows 7/8.1/10/11  

## Requirements

- **MSYS2:** For compiling the C source files.  
  Download and install MSYS2 from [msys2.org](https://www.msys2.org/).  
  For a tutorial on installing MSYS2 with VSCode, see [this guide](https://code.visualstudio.com/docs/cpp/config-mingw).
- **GCC:** Available within MSYS2.
- **OpenSSL:** Ensure OpenSSL is installed and available in your MSYS2 environment.  
  **Note:** For OpenSSL to function properly, you must have `libssl-3-x64.dll` and `libcrypto-3-x64.dll` available.
- **Python 3:** Used for running the encryption and router generation scripts.
- **PHPDesktop:** Download from [phpdesktop](https://github.com/cztomczak/phpdesktop) to run your encrypted application.

## Folder Structure

The project is divided into two main parts:

### 1. The Compiler (phpdesktop-encrypter)

<pre>
phpdesktop-encrypter/
├── .vscode/  
|   ├── c_cpp_properties.json        
|   ├── settings.json                  
│   └── task.json               
├── input_docs/
│   ├── components/
|   |   ├── footer.php
|   |   ├── header.php
|   |   ├── navbar.php
|   |   └── sidebar.php  
|   ├── pages/  
|   |   ├── home.php
|   |   ├── contact.php
|   |   └── license.php
|   └── public/  
|       └── index.php      
├── output_docs/               
│   ├── components/
|   |   ├── footer.php.enc
|   |   ├── header.php.enc
|   |   ├── navbar.php.enc
|   |   └── sidebar.php.enc  
|   ├── pages/  
|   |   ├── home.php.enc
|   |   ├── contact.php.enc
|   |   └── license.php.enc
|   ├── public/  
|   |    └── index.php.enc     
│   ├── router.php       
│   └── phpdesktop-encrypter.exe 
├── scripts/         
|   ├── phpdesktop-encrypter.c
|   ├── generate_router.py  
|   ├── generate_map.py       
|   └── generate_router.php            
├── compile.bat                                                                
└── README.md
</pre>


### 2. PHPDesktop (Production Version)

After compiling, the production version of your PHPDesktop application will have this structure:

<pre>

PHP Version 7.1

phpdesktop/
├── www/                             
│   ├── components/
|   |   ├── footer.php.enc
|   |   ├── header.php.enc
|   |   ├── navbar.php.enc
|   |   └── sidebar.php.enc  
|   ├── pages/  
|   |   ├── home.php.enc
|   |   ├── contact.php.enc
|   |   └── license.php.enc
|   ├── public/  
|   |    └── index.php.enc     
│   └── router.php       
├── phpdesktop-chrome.exe               
├── phpdesktop-encrypter.exe 
├── libssl-3-x64.dll
├── libcrypto-3-x64.dll
└── settings.json
</pre>

<pre>

PHP Version 8.3

phpdesktop/
├── php/                             
|   ├── libssl-3-x64.dll
|   └── libcrypto-3-x64.dll
├── www/                             
│   ├── components/
|   |   ├── footer.php.enc
|   |   ├── header.php.enc
|   |   ├── navbar.php.enc
|   |   └── sidebar.php.enc  
|   ├── pages/  
|   |   ├── home.php.enc
|   |   ├── contact.php.enc
|   |   └── license.php.enc
|   ├── public/  
|   |    └── index.php.enc     
│   └── router.php       
├── phpdesktop-chrome.exe               
├── phpdesktop-encrypter.exe 
└── settings.json
</pre>


In production, all PHP files are encrypted/obfuscated (with the `.enc` extension), and `router.php` decrypts and evaluates them at runtime.

## Usage

### Compiling the Encrypter

1. **Prepare the Environment:**  
   - Open MSYS2.
   - Ensure you have Python 3 and GCC installed.
   - Make sure OpenSSL (with `libssl-3-x64.dll` and `libcrypto-3-x64.dll`) is available in your PATH.

2. **Run `compile.bat`:**  
   Open a Command Prompt and run the provided `compile.bat` script from the root of the `phpdesktop-encrypter` folder. This script will:
   - Encrypt and obfuscate all PHP files (both pages and components) from `input_docs` and generate a map (`map.json`).
   - Copy the base router file (`generate_router.php`) to `output_docs`.
   - Generate `router.php` using the embedded map.
   - Compile `phpdesktop-encrypter.c` into `phpdesktop-encrypter.exe`.

### Integrating with PHPDesktop

1. **Copy the Output Files:**  
   After compilation, copy the entire `output_docs` folder into the `www` folder of your PHPDesktop project (or set your PHPDesktop configuration to point to `www`).

2. **PHPDesktop Settings:**  
   Modify your `settings.json` file in PHPDesktop to use `router.php` as the index file. For example:

   ```json
   "web_server": {
       "listen_on": ["127.0.0.1", 0],
       "www_directory": "www",
       "index_files": ["router.php"],
       "cgi_interpreter": "php/php-cgi.exe",
       "cgi_extensions": ["php"],
       "cgi_temp_dir": "",
       "404_handler": "/pretty-urls.php",
       "hide_files": []
   },

3. **PHPDesktop Settings:**  
   Modify your `php.ini` file in PHPDesktop to use `opcache`. For example:

<pre>
  zend_extension=php_opcache.dll
</pre>

4. **Run PHPDesktop:**  
Launch phpdesktop-chrome.exe (or your chosen PHPDesktop executable). The router (router.php) will decrypt and evaluate the encrypted PHP files at runtime.

# PHPDesktop Settings
Make sure to update your PHPDesktop settings.json as shown above to point to the correct www directory and index file (router.php). This ensures that all requests are routed through the decryption and evaluation layer.

# License
This project is licensed under the MIT License.


