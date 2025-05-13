Collecting requests==2.31.0 (from -r D:\snapchatADAPI-py\requirements.txt (line 1))
  Obtaining dependency information for requests==2.31.0 from https://files.pythonhosted.org/packages/70/8e/0e2d847013cb52cd35b38c009bb167a1a26b2ce6cd6965bf26b47bc0bf44/requests-2.31.0-py3-none-any.whl.metadata
  Downloading requests-2.31.0-py3-none-any.whl.metadata (4.6 kB)
Collecting python-dotenv==1.0.0 (from -r D:\snapchatADAPI-py\requirements.txt (line 2))
  Obtaining dependency information for python-dotenv==1.0.0 from https://files.pythonhosted.org/packages/44/2f/62ea1c8b593f4e093cc1a7768f0d46112107e790c3e478532329e434f00b/python_dotenv-1.0.0-py3-none-any.whl.metadata
  Downloading python_dotenv-1.0.0-py3-none-any.whl.metadata (21 kB)
Collecting pydantic==2.4.2 (from -r D:\snapchatADAPI-py\requirements.txt (line 3))
  Obtaining dependency information for pydantic==2.4.2 from https://files.pythonhosted.org/packages/73/66/0a72c9fcde42e5650c8d8d5c5c1873b9a3893018020c77ca8eb62708b923/pydantic-2.4.2-py3-none-any.whl.metadata
  Downloading pydantic-2.4.2-py3-none-any.whl.metadata (158 kB)
     ------------------------------------ 158.6/158.6 kB 474.5 kB/s eta 0:00:00
Collecting charset-normalizer<4,>=2 (from requests==2.31.0->-r D:\snapchatADAPI-py\requirements.txt (line 1))
  Obtaining dependency information for charset-normalizer<4,>=2 from https://files.pythonhosted.org/packages/27/f2/4f9a69cc7712b9b5ad8fdb87039fd89abba997ad5cbe690d1835d40405b0/charset_normalizer-3.4.1-cp313-cp313-win_amd64.whl.metadata
  Downloading charset_normalizer-3.4.1-cp313-cp313-win_amd64.whl.metadata (36 kB)
Collecting idna<4,>=2.5 (from requests==2.31.0->-r D:\snapchatADAPI-py\requirements.txt (line 1))
  Obtaining dependency information for idna<4,>=2.5 from https://files.pythonhosted.org/packages/76/c6/c88e154df9c4e1a2a66ccf0005a88dfb2650c1dffb6f5ce603dfbd452ce3/idna-3.10-py3-none-any.whl.metadata
  Downloading idna-3.10-py3-none-any.whl.metadata (10 kB)
Collecting urllib3<3,>=1.21.1 (from requests==2.31.0->-r D:\snapchatADAPI-py\requirements.txt (line 1))
  Obtaining dependency information for urllib3<3,>=1.21.1 from https://files.pythonhosted.org/packages/6b/11/cc635220681e93a0183390e26485430ca2c7b5f9d33b15c74c2861cb8091/urllib3-2.4.0-py3-none-any.whl.metadata
  Downloading urllib3-2.4.0-py3-none-any.whl.metadata (6.5 kB)
Collecting certifi>=2017.4.17 (from requests==2.31.0->-r D:\snapchatADAPI-py\requirements.txt (line 1))
  Obtaining dependency information for certifi>=2017.4.17 from https://files.pythonhosted.org/packages/38/fc/bce832fd4fd99766c04d1ee0eead6b0ec6486fb100ae5e74c1d91292b982/certifi-2025.1.31-py3-none-any.whl.metadata
  Downloading certifi-2025.1.31-py3-none-any.whl.metadata (2.5 kB)
Collecting annotated-types>=0.4.0 (from pydantic==2.4.2->-r D:\snapchatADAPI-py\requirements.txt (line 3))
  Obtaining dependency information for annotated-types>=0.4.0 from https://files.pythonhosted.org/packages/78/b6/6307fbef88d9b5ee7421e68d78a9f162e0da4900bc5f5793f6d3d0e34fb8/annotated_types-0.7.0-py3-none-any.whl.metadata
  Downloading annotated_types-0.7.0-py3-none-any.whl.metadata (15 kB)
Collecting pydantic-core==2.10.1 (from pydantic==2.4.2->-r D:\snapchatADAPI-py\requirements.txt (line 3))
  Downloading pydantic_core-2.10.1.tar.gz (347 kB)
     -------------------------------------- 347.3/347.3 kB 1.9 MB/s eta 0:00:00
  Installing build dependencies: started
  Installing build dependencies: finished with status 'done'
  Getting requirements to build wheel: started
  Getting requirements to build wheel: finished with status 'done'
  Preparing metadata (pyproject.toml): started
  Preparing metadata (pyproject.toml): finished with status 'error'

  error: subprocess-exited-with-error
  
  Preparing metadata (pyproject.toml) did not run successfully.
  exit code: 1
  
  [6 lines of output]
  
  Cargo, the Rust package manager, is not installed or is not on PATH.
  This package requires Rust and Cargo to compile extensions. Install it through
  the system's package manager or via https://rustup.rs/
  
  Checking for Rust toolchain....
  [end of output]
  
  note: This error originates from a subprocess, and is likely not a problem with pip.
error: metadata-generation-failed

Encountered error while generating package metadata.

See above for output.

note: This is an issue with the package mentioned above, not pip.
hint: See above for details.

[notice] A new release of pip is available: 23.2.1 -> 25.0.1
[notice] To update, run: python.exe -m pip install --upgrade pip
