# dmx-documentation

This project is for everything concerning DMX software documentation. You can find the documentation at https://docs.dmx.systems/.

To build the html files locally, install Sphinx and the readthedocs theme.

Example for Ubuntu:

```bash
sudo apt install python-pip python-sphinx
pip install sphinx_rtd_theme
git clone https://git.dmx.systems/dmx-platform/dmx-documentation.git
cd dmx-documentation/docs
make html
```
The HTML files then are in _build/html.
