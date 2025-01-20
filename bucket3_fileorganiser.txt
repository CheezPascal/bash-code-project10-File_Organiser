#!/bin/bash

# Function to print usage instructions
usage() {
  echo "Usage: $0 <folder_path>"
  exit 1
}

# Check if a folder path is provided
if [ -z "$1" ]; then
  echo "Error: No folder path provided."
  usage
fi

# Get the folder path from the input
FOLDER_PATH="$1"

# Check if the folder exists
if [ ! -d "$FOLDER_PATH" ]; then
  echo "Error: The provided path is not a valid folder."
  exit 1
fi

# Notify the user that the folder is being read
echo "Reading contents of the folder: $FOLDER_PATH"

# Read the contents of the folder
FILES=$(find "$FOLDER_PATH" -type f)

# Notify the user that the script is ready to begin
if [ -z "$FILES" ]; then
  echo "No files found in the folder to organize. Exiting."
  exit 0
fi

echo "Files have been read. Starting the organization process..."

# Initialize a list to track actions
ACTIONS=()

# Loop through each file
for FILE in $FILES; do
  # Get the modification year and month of the file
  YEAR=$(date -r "$FILE" +%Y)
  MONTH=$(date -r "$FILE" +%B)

  # Create the target directory if it doesn't exist
  TARGET_DIR="$FOLDER_PATH/$YEAR/$MONTH"
  if [ ! -d "$TARGET_DIR" ]; then
    mkdir -p "$TARGET_DIR"
    ACTIONS+=("Created directory: $TARGET_DIR")
  fi

  # Move the file to the target directory
  mv "$FILE" "$TARGET_DIR/"
  ACTIONS+=("Moved file: $FILE -> $TARGET_DIR")

done

# Notify the user of completion
echo "Organization process completed."

# Print the list of actions
echo "Summary of actions:"
for ACTION in "${ACTIONS[@]}"; do
  echo "- $ACTION"
done