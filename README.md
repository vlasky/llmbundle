# Overview

`llmbundle` is a utility to bundle files and directories into a self-contained, executable bash script that can recreate the original file structure with accurate permissions and contents. It is intended for sharing sets of files with Large Language Models (LLMs) for content modification and analysis.

The generated bash script includes clear instructions for LLMs, making it easy for them to understand and modify file contents whilst preserving the overall structure.

The bundling technique is based on bash heredocs (here documents). It is intended to be easily readable by humans and LLMs.

## Features

- Packages multiple files and directories into a single, self-contained bash script
- Preserves directory structure and file permissions
- Supports both text and binary files (using base64 encoding for binary files)
- Supports wildcard patterns for file selection
- Handles nested here-documents in file contents
- Includes clear instructions for LLMs in the generated output
- Can write output directly to a file or stdout

## Installation

```bash
git clone https://github.com/yourusername/llmbundle.git
cd llmbundle
chmod +x llmbundle
sudo cp llmbundle /usr/local/bin/   # or another directory in your PATH
```

## Usage

```bash
llmbundle [-o output_file] [file1] [file2] ... [fileN]
```

## Options

`-o output_file`: Write the generated script to a file instead of stdout. The output file will be made executable.

### Examples
 Package specific files and output to stdout:
```bash
llmbundle file1.txt file2.txt script.sh
```
Package files and save to a script:
```bash
llmbundle -o backup.sh file1.txt file2.txt script.sh
```
Package all files in a directory:
```bash
llmbundle -o project_backup.sh myproject/*
```
Package files matching a pattern:
```bash
llmbundle -o src_backup.sh src/*.py config/*.json
```

### Use Case with LLMs

1. Bundle your files:
```bash
llmbundle -o project.sh myproject/*
```

2. Give the LLM a prompt to help it know what to do with the bundle, for example:

   "The following is a self-extracting bash script generated by llmbundle. The script bundles files encoded as here-documents. When executed, it will recreate these files. Please analyze the file contents in this bundle. Treat the outer script structure as a container - only the contents between the EOF delimiters should be modified. The script already includes detailed instructions for LLMs in its header comments."

3. Open project.sh in a text editor and copy its contents into your LLM conversation.

4. Prompt the LLM to analyse and/or modify the file contents as needed.

5. When you are done, prompt the LLM to generate the modifed bundle, for example:

   "Please generate the complete bundle script incorporating all the changes we discussed. Keep all container script syntax, headers, and EOF delimiters exactly as they were in the original bundle. Only the file contents between EOF delimiters 
should reflect our modifications. Format the output as a single code block that I can directly save and execute."

6. Inspect the output for correctness, then copy the modified script from the LLM's response.

7. Save to a file and execute to recreate your files with the LLM's modifications:
```bash
chmod +x modified_script.sh
./modified_script.sh
```

## ⚠️ Important Security Considerations

* Be cautious when sharing scripts containing confidential/sensitive information

* Before running any bundle script that has been modified by an LLM:

1. **Always inspect the generated script** before execution:
   - Check that the script structure hasn't been corrupted
   - Verify that only the intended file contents were modified
   - Ensure no unexpected commands were inserted
   - Pay special attention to any script files that were part of the bundle

2. **Use a safe execution environment**:
   - Run modified scripts in an isolated directory
   - Consider using a separate user account
   - Back up any existing files in the target directory
   - Run initially with files that can be safely overwritten

3. **Best Practices**:
   - Never run modified bundles in production environments
   - Test with non-critical files first
   - Use version control to track changes
   - Keep original copies of important files
   - Be especially careful with bundles containing executable files or scripts

4. **Remember** - while LLMs are powerful tools for content modification, they can sometimes:
   - Misunderstand the script structure
   - Insert unexpected modification
   - Corrupt the bundle format
   - Generate invalid syntax
   - Misinterpret shell scripts contained within the bundle

The safest approach is to always treat LLM-modified scripts as untrusted input until verified.

## Limitations

* Generated script size is proportional to the total size of bundled files
* Bundled binary files increase in size due to base64 encoding
* All file contents are stored in memory during script generation


## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Disclaimer

As per the MIT License, this software is provided "as is", without warranty of any kind, 
express or implied, including but not limited to the warranties of merchantability, 
fitness for a particular purpose and noninfringement.

Additional notes specific to this software:
- You are responsible for reviewing and testing any scripts generated or modified through 
  interaction with LLMs
- You understand the risks of executing scripts that have been modified by LLMs
- You should take appropriate precautions including backing up data and using isolated 
  test environments

## Acknowledgements

Developed through conversation with Anthropic's Claude 3.5 Sonnet AI assistant.
