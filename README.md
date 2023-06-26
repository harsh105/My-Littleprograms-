#!/bin/bash

directory_path="your/directory/path"  # Replace with the specific directory path
keywords=("keyword1" "keyword2" "keyword3")  # Replace with the keywords you want to search

output_file="output.txt"  # Specify the output file name

# Check if the specified directory exists and is a directory
if [ -d "$directory_path" ]; then
    # Loop over each keyword
    for keyword in "${keywords[@]}"; do
        # Find all Java files in the directory
        java_files=$(find "$directory_path" -type f -name "*.java")

        # Loop over each Java file
        for file in $java_files; do
            # Search for the keyword in the file and append matching lines to the output file
            grep -H "$keyword" "$file" >> "$output_file"
        done
    done

    echo "Search completed. Matching lines saved in $output_file."
else
    echo "The specified directory does not exist."
fi
