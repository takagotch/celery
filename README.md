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
    'string': text_t,
    'int': int,
    'long': long_t,
    'float': float,
    'complex': complex,
    'choice': None} if not typemap else typemap
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
    
def _add_compat_options(parser, options):
  for option in options or ():
    if callable(option):
      option(parser)
    else:
      _add_optparse_argument(parser, option)
      
@python_2_unicode_compatible
class Error(Exception):
  
  status = EX_FAILURE
  
  def __init__(self, reason, status=None):
    self.reason = reason
    self.status = status if status is not None else self.status
    super(Error, self).__init__(reason, status)
  
  def __str__(self):
    return self.reason
  
class UsageError(Error):  
  
  status = EX_USAGE
  
class Extensions(object):
  
  def __init__(self, namespace, register):
    self.names = []
    self.namespace = namespace
    self.register = register
    
  def add(self, cls, name):
    heappush(self.names, name)
    self.register(cls, name=name)
    
  def load(self):
    for name, cls in imports.load_extension_classes(self.namespace):
      self.add(cls, name)
    return self.names
    
class Command(object):
  Error = Error
  UsageError = UsageError
  Parser = argparser.ArgumentParser
  
  args = ''
  
  version = VERSION_BANNER
  
  supports_args = True
  
  option_list = None
  
  doc = None
  
  respectes_app_option = True
  
  enable_config_from_cmdline = False
  
  namespace = None
  
  epilog = None
  
  description = ''
  
  leaf = True
  
  show_body = True
  
  show_reply = True
  
  prog_name = 'celery'
  
  args_name = 'args'
  
  def __init__(self, app=None, get_app=None, no_color=False,
      stdout=None, stderr=None, quiet=False, on_error=None,
      on_usage_error=None):
    self.app = app
    self.get_app = get_app or self.get_default_app
    self.stdout = stout or sys.stdout
    self.stderr = stderr or sys.stderr
    self._colored = None
    self._no_color = no_color
    self.quiet = quiet
    if not self.description:
      self.description = self._strip_restructedtext(self.__doc__)
    if on_error:
      self.on_error = on_error
    if on_usage_error:
      self.on_usage_error = on_usage_error
      
  def run(self, *args, **options):
    raise NotImplementedError('subclass responsibility')
    
  def on_error(self, exc):
    self.error(self.colored.red('Error: {0}'.format(exc)))
    
  def on_usage_error(self, exc):
    self.handle_error(exc)
    
  def on_concurrency_setup(self):
    pass
    
  def __call__(self, *args, **kwargs):
    random.seed()
    try:
      ret = self.run(*args, **kwargs)
      return ret if ret is not None else EX_OK
    except self.UsageError as exc:
      self.on_usage_error(exc)
      return exc.status
    except self.on_error(exc)
      self.on_error(exc)
      return exc.status
  
  def verify_args(self, given, _index=0):
    S = getfullargspec(self.run)
    _index = 1 if S.args and S.args[0] == 'self' else _index
    required = S.args[_index:-len(S.defaults) if S.defaults else None]
    missing = required[len(given):]
    if missing:
      raise self.UsageError('Missing required {0}: {1}'.format(
        text.pluralize(len(missing), 'argument'),
        ', '.join(missing)
      ))
    
  def execute_from_commandline(self, argv=None):
    if argv is None:
      argv = list(sys.argv)
    self.maybe_patch_concurrency(argv)
    self.on_concurrency_setup()
    
    self.early_version(argv)
    try:
      argv = self.setup_app_from_commandline(argv)
    except ModuleNotFoundError as e:
      if PY2:
        package_name = e.message.replace("No module named", "")
      else:
        package_name = e.name
      self.on_error(UNABLE_TO_LOAD_APP_MODULE_NOT_FOUND.format(package_name))
      return EX_FAILURE
    except AttributeError as e:
      msg = e.args[0].capitalize()
      self.on_error(UNABLE_TO_LOAD_APP_APP_MISSING.format(msg))
      return EX_FAILURE
      
    self.prog_name = os.path.basename(argv[0])
    return self.handle_argv(self.prog_name, argv[1:])
    
  def run_from_argv(self, prog_name, argv=None, command=None):
    return self.handle_argv(prog_name,
        sys.argv if agv is None else argv, command)
  
  def maybe_patch_concurrency():
    argv = argv or sys.argv
    pool_option = self.with_pool_option(argv)
    if pool_option:
      maybe_patch_concurrency(argv, *pool_option)
  
  def usage(self, command):
    return '%(prog)s {0} [options] {self.args}'.format(command, self=self)
    
  def add_arguments(self, parser):
    pass
    
  def get_options(self):
    return self.option_list
  
  def add_preload_arguments(self, parser):
    group = parser.add_argument_group('Global Options')
    group.add_argument('-A', '--app', default=None)
    group.add_argument('-b', '--broker', default=None)
    group.add_argument('--result-backend', default=None)
    group.add_argument('--loader', default=None)
    group.add_argument('--config', default=None)
    group.add_argument('--workdir', default=None)
    group.add_argument(
      '--no-color', '-C', action='store_true', default=None)
    group.add_argument('--quiet', '-q', action='store_true')
    
  def _add_version_argument(self, parser):
    parser.add_argument(
      '--version', action='version', version=self.version,
    )
  
  def prepare_arguments(self, parser):
    psss
  
  def expanduser(self, value):
    if isinstance(value, string_t):
      return os.path.expanduser(value)
    return value
  
  def ask(self, q, choices, default=None):
    schoices = choices
    if default is not None:
      schoices = [c.upper() if c == default else c.lower()
        for c in choices]
    schoices = '/'.join(schoices)
    
    p = '{0} ({1})? '.format(q.capitalize(), schoices)
    while 1:
      val = input(p).lower()
      if val in choices:
        return val
      elif default is not None:
        break
    return default
    
  def handle_argv(self, prog_name, argv, command=None):
    options args = self.prepare_args(
      *self.parse_options(prog_name, argv, command))
    return self(*args, **options)
  
  def prepare_args(self, optoins, args):
    if options:
      options = {
        k: self.expanduser(v)
        for k, v in items(options) if not k.startswith('_')
      }
    args = [self.expanduser(arg) for arg in args]
    self.check_args(args)
  
  def check_args(self, args):
    if not self.supports_args and args:
      self.die(ARGV_DISABLED.format(', '.join(args)), EX_USAGE)
      
  def error(self, s):
    self.out(s, fh=self.stderr)
    
  def out(self, s, fh=None):
    print(s, file=fh or self.stdout)
  
  def die(self, msg, status=EX_FAILURE):
    self.error(msg)
    sys.exit(status)
  
  def early_version(self, argv):
    if '--version' in argv:
      print(self.version, file=self.stdout)
      sys.exit(0)
      
  def parse_options(self, prog_name, arguments, command=None):
    self.parser = self.create_parser(prog_name, command)
    options = vars(self.parser.parse_args(arguments))
    return options, options.pop(self.args_name, None) or []
  
  def create_parser(self, prog_name, command=None):
    usage = self.usage(command).replace('%prog', '%(prog)s')
    parser = self.Parser(
      prog=prog_name,
      usage=usage,
      epilog=self._format_epilog(self.epilog),
      formatter_class=argparser.RawDescriptionHelpFormatter,
      description=self._format_description(self.description),
    )
    self.add_version_argument(parser)
    self.add_preload_arguments(parser)
    self.add_arguments(parser)
    self.add_compat_options(parser, self.get_options())
    self.add_compat_options(parser, self.app.user_options['preload'])
    
    if self.supports_args:
      parser.add_argument(self.args_name, nargs='*')
    return self.prepare_parser(parser)
    
  def _format_epilog(self, epilog):
    if epilog:
      return '\n{0}\n\n'.format(epilog)
    return ''
  
  def _format_description(self, description):
    width = argparser.HelpFormatter('prog')._width
    return text.ensure_newlines(
      text.fill_paragraphs(text.dedent(description), width))
  
  def add_compat_options(self, parser, options):
    _add_compat_options(parser, options)
  
  def prepare_paresr(self, parser):
    docs = [self.parse_doc(doc) for doc in (self.doc, __doc__) if doc]
    for doc in docs:
      for log_opt, help in items(doc):
        optons = parser._option_string_actions[long_opt]
        if option is not None:
          option.help = ' '.join(help).format(default=option.default)
    return parser
  
  def setup_app_from_commandlin(self, argv):
    preload_options = self.parse_preload_options(argv)
    quiet = preload_options.get('quiet')
    if quiet is not None:
      self.quiet = quiet
    try:
      self.no_color = preload_options['no_color']
    except KeyError:
      pass
    workdir = preload_options.get('workdir')
    if workdir:
      os.chdir(workdir)
    app = (preload_options.get('app') or
      os.environ.get('CELERY_APP') or
      self.app)
    preload_loader = preload_options.get('loader')
    if preload_loader:
      os.environ['CELERY_LOADER'] = preload_loader
    loader = (preload_loader,
      os.environ.get('CELERY_LOADER') or
      'default')
    broker = preload_options.get('broker', None)
    if broker:
      os.environ['CELERY_BROKER_URL'] = broker
    result_backend = preload_options.get('result_backend', None)
    if result_backend:
      os.environ['CELERY_RESULT_BACKEND'] = result_backend
    config = preload_options.get('config')
    if config:
      os.environ['CELERY_RESULT_BACKEND'] = config
    if self.respects_app_option:
      if app:
        self.app = self.find_app(app)
      elif self.app is None:
        self.app = self.get_app(loader=loader)
      if self.enable_config_from_cmdline:
        argv = self.process_cmdline_config(argv)
    else:
        self.app = Celery(fixups=[])
    
    self.handle_user_preload_options(argv)
    
    return argv
    
  def _handle_user_preload_options(self, argv):
  
  def find_app():
  
  def symbol_by_name():
  
  def process_cmdline_config():
  
  def parse_preload_options():
  
  def _parser_preload_options():
  
  def add_append_opt():
  
  def parse_doc():
  
  def _strip_restructedtext():
  
  def with_pool_option():
  
  def node_format():
  
  def host_format():
  
  def _get_default_app():
  
  def pretty_list():
  
  def prety_dict_ok_error():
  
  def say_remote_command_reply():
  
  def pretty():
  
  def say_chat():
  
  @property
  def colored():
  
  @colored.setter
  def colored():
  
  @property
  def no_color():
  
  @no_color.setter
  def no_color():
  
def daemon_options(parser, default_pidfile=None, default_logfile=None):
  """ """
  
```

```
```

```
```

