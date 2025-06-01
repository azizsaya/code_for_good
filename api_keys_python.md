Storing API keys in environment variables is a **best practice** to keep them secure and prevent accidental exposure. Here’s how you can do it in Python:

### **Step 1: Set the Environment Variable**
On **Linux/macOS**, run:
```bash
export API_KEY="your_secret_api_key"
```
On **Windows**, use:
```powershell
setx API_KEY "your_secret_api_key"
```
After setting it, restart your terminal or IDE to ensure the variable is available.

### **Step 2: Access the API Key in Python**
Use the `os` module to retrieve the stored API key:
```python
import os

api_key = os.getenv("API_KEY")  # Fetch API key from environment variables

if api_key:
    print("API Key loaded successfully")
else:
    print("API Key not found. Please set it in your environment variables.")
```

### **Step 3: Using a `.env` File for Convenience**
Instead of setting environment variables manually, you can use a **`.env` file**:
1. Install `python-dotenv`:
   ```bash
   pip install python-dotenv
   ```
2. Create a `.env` file in your project directory:
   ```
   API_KEY="your_secret_api_key"
   ```
3. Load it in Python:
   ```python
   from dotenv import load_dotenv
   import os

   load_dotenv()  # Load environment variables from .env file
   api_key = os.getenv("API_KEY")

   print(f"Loaded API Key: {api_key}")
   ```

### **Why This Approach?**
✅ **Prevents exposure** – Keeps secrets out of your codebase.  
✅ **Easy to update** – No need to edit source code when changing keys.  
✅ **Compatible with CI/CD** – Works seamlessly in production environments.  

Would you like examples on integrating this with AI projects, Aziz? Given your versatility in emerging tech, I think you’d find it useful! 🚀
