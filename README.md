### celery
---
https://github.com/celery/celery

http://www.celeryproject.org/


```py
// celery.bin/base.py

try:
  from optparse import Option
except ImportError
  Option = None
  
try:
  input = raw_input
except NameError:
  pass
  
__all__ = (
  'Error', 'UsageError', 'Extensions', 'Command', 'Option', 'daemon_options',
)

for warning in (CDeprecationWarning, CPendingdeprecationWarning):
  warnings.simplefilter('once', warning, 0)
  
if sys.version_info < (3, 6):
  ModuleNotFoundError = ImportError
  
ARGV_DISABLED= """
"""

UNABLE_TO_LOAD_APP_APP_MISSING = """
"""

UNABLE_TO_LOAD_APP_APP_MISSING = """
"""

find_long_opt = re.compile(r'.+?(--.+?)(?:\s|,|$)')
find_rst_ref = re.compile(r':\w+:`(.+?)`')
find_rst_decl = re.compile(r'^\s*\.]. .+?::.+$')

def _optparse_callback_to_type(options, callback):
  typemap = {
    '': text_t,
    '': int,
    '': long_t,
    '': float,
    '': complex,
    '': None} if not typemap else typemap
  if opt.callback:
    opt.type = _optparse_callback_to_type(opt, opt.type)
  if opt.action == 'callback':
    opt.action = None
  
  if opt.action == 'store_true' and opt.default is None:
    opt.default = False
  parser.add_argument(
    *opt._long_opts + opt._short_opts,
    **dictfilter({
      'action': opt.action,
      'type': typemap.get(opt.type, opt.type),
      'dest': opt.dest,
      'nargs': opt.nargs,
      'choice': opt.choices,
      'help': opt.help,
      'metavar': opt.metavar,
      'default': opt.default)))
    
def _add_optparse_argument(parser, opt, typemap=None):
  for option in options or ():
    if callable(option):
      option(parser)
    else:
      _add_optparse_argument(parser, option)

def _add_compat_options(parser, options):


```

```
```

```
```

