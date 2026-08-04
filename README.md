Cloudinary Preload Notebook

This project contains a Jupyter notebook that:

- Queries Akamai Reporting API for IVM video URLs.
- Extracts hostname and path from each returned URL.
- Sends HEAD requests for sample URLs to trigger downstream preload checks.
- Supports Akamai staging override for validation.

Project Files

- pre-load-cloudinary.ipynb: Main notebook workflow.
- pyproject.toml: Project metadata and dependencies.
- main.py: Optional local Python entry file.

Requirements

- Python 3.14 or newer.
- Access to an Akamai API client configured in ~/.edgerc.
- Reporting API permissions for your API client.
- uv package manager.

Install uv

Choose one option based on your OS.

macOS and Linux (official installer):

    curl -LsSf https://astral.sh/uv/install.sh | sh

Homebrew (macOS):

    brew install uv

Windows (PowerShell, official installer):

    powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"

After installation, verify:

    uv --version

Set Up the Project

From the project root:

    uv sync

This creates a local virtual environment and installs dependencies from pyproject.toml.

You can then start Jupyter:

    uv run jupyter lab preload-cloudinary.ipynb

or

    uv run jupyter notebook preload-cloudinary.ipynb

Akamai Credentials Setup

1. Ensure ~/.edgerc exists and contains the section you want to use.
2. Set the section as an environment variable before launching Jupyter.

Important: The section must be set as an environment variable.

Example:

    export AKAMAI_EDGEGRID_SECTION=your-section-name

Optional account switch key:

    export AKAMAI_ACCOUNT_SWITCH_KEY="F-AC-xxxxxxx:x-xxxx"

Minimal ~/.edgerc section example:

    [your-section-name]
    host = akab-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.luna.akamaiapis.net
    client_token = akab-client-token
    client_secret = your-client-secret
    access_token = akab-access-token

Important Notes About Environment Variables and Jupyter

- Jupyter kernels only inherit environment variables available when the kernel starts.
- If you export AKAMAI_EDGEGRID_SECTION or AKAMAI_ACCOUNT_SWITCH_KEY after the kernel is already running, restart the notebook kernel.
- If needed, set account_switch_key inside a notebook cell as a temporary override for that session.

Run the Notebook

1. Open pre-load-cloudinary.ipynb.
2. Run cells in order.
3. Verify the EdgeGrid setup cell prints the expected host.
4. Run the report cell to build URL targets.
5. Run the final HEAD-check cell to test first N URLs against staging IP using curl --resolve.

Current staging override used in the notebook:

- 104.103.169.11

What the Notebook Produces

- video_targets: list of dictionaries with hostname and path.
- results: list of dictionaries with:
  - hostname
  - path
  - status_code
  - content_type

Troubleshooting

No video targets returned

- Confirm AKAMAI_EDGEGRID_SECTION is set correctly before starting Jupyter.
- Confirm your API client has access to the account and report data.
- Confirm the time range used in the report cell has available data.

401 with accountSwitchKey

- Some API clients are not provisioned for account switching.
- Use credentials that are authorized for multi-account context, or run without accountSwitchKey when appropriate.

curl errors in final cell

- Ensure curl is available in your environment.
- Validate staging IP and DNS override format.
- Increase timeout values in the final cell for slow paths.

Development Notes

- Dependencies are managed with uv and declared in pyproject.toml.
- Python requirement is 3.14+.
- Notebook is the source of truth for this workflow.
