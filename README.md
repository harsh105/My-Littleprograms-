#!/bin/bash

# Check if text file and workspace directory are provided
if [ "$#" -ne 2 ]; then
  echo "Usage: $0 <repositories_file> <workspace_directory>"
  exit 1
fi

# Arguments
REPO_FILE=$1
WORKSPACE_DIR=$2

# Check if the text file exists
if [ ! -f "$REPO_FILE" ]; then
  echo "File $REPO_FILE not found!"
  exit 1
fi

# Create the workspace directory if it doesn't exist
mkdir -p "$WORKSPACE_DIR"

# Loop through each line in the file
while IFS=',' read -r REPO_URL FEATURE_BRANCH; do
  # Skip empty lines
  if [ -z "$REPO_URL" ] || [ -z "$FEATURE_BRANCH" ]; then
    echo "Skipping invalid entry in file."
    continue
  fi
  
  echo "Cloning $REPO_URL into $WORKSPACE_DIR..."

  # Clone the repository into the workspace directory
  git clone "$REPO_URL" "$WORKSPACE_DIR/$(basename "$REPO_URL" .git)"

  # Extract repository name and navigate to it
  REPO_NAME=$(basename "$REPO_URL" .git)
  cd "$WORKSPACE_DIR/$REPO_NAME" || exit

  # Create and switch to the new feature branch
  echo "Creating and switching to branch $FEATURE_BRANCH..."
  git checkout -b "$FEATURE_BRANCH"

  # Navigate back to the workspace directory
  cd "$WORKSPACE_DIR" || exit

done < "$REPO_FILE"

echo "All repositories cloned and branches created successfully."
