# Generate Markdown Files Rule

When creating AWS SAA-C03 service notes:

1. **File Naming**: Use parcal case with hyphens: `Service-Name.md` (e.g., `EC2.md`, `S3.md`, `Api-Gateway.md`)

2. **File Location**: Save in `c:\Users\401859\OneDrive - Delta Air Lines\Documents\AIF-C01\`

3. **Generate File**: Check if a file already exists or create the .md file with complete content following the aif-c01-template.md template

4. **Ask Confirmation**: Ask for permission to update the existing file or generate the file immediately without asking for permission

5. **Folder Structure**: Classify the file into broad level category of the service and place the file in appropriate folder. Create the folder immediately without asking for permission if it doesn't exists

6. **Update INDEX.md**: After creating a new file, automatically update `INDEX.md` in the root directory by adding the new file link to the appropriate category section. Increment the file count for that category.