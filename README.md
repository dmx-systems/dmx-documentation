# DMX Documentation

Documentation for the DMX platform, as available at https://docs.dmx.systems/.

To build the html files locally, install Sphinx and the readthedocs theme.

Example for Ubuntu:

```bash
sudo apt install python-pip python-sphinx
pip install sphinx_rtd_theme
git clone https://git.dmx.systems/dmx-platform/dmx-documentation.git
cd dmx-documentation
make html
```

The HTML files then are in `docs/_build/html`.
