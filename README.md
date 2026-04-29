# inko-knowndirs

An [Inko](https://inko-lang.org/) library for locating platform-specific standard directories. Inspired by Rust crates such as [`dirs`](https://crates.io/crates/dirs) and [`etcetera`](https://crates.io/crates/etcetera).

## Supported standards

- [macOS Standard Directories](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html#//apple_ref/doc/uid/TP40010672-CH2-SW6) (`knowndirs.apple`)
- [XDG Base Directory Specification](https://specifications.freedesktop.org/basedir/latest/) (`knowndirs.xdg`)

## Usage

### Apple

```inko
import knowndirs.apple (Locator)

let locator = Locator.new.or_panic_with('HOME must be set')

locator.application_support_directory # => $HOME/Library/Application Support
locator.caches_directory              # => $HOME/Library/Caches

let app = locator.for_entry('com.example.MyApp')

app.application_support_directory # => $HOME/Library/Application Support/com.example.MyApp
app.caches_directory              # => $HOME/Library/Caches/com.example.MyApp
```

### XDG

```inko
import knowndirs.xdg (Locator)

let locator = Locator.new.or_panic_with('HOME must be set')

locator.data_home          # => $XDG_DATA_HOME   or $HOME/.local/share
locator.config_home        # => $XDG_CONFIG_HOME or $HOME/.config
locator.state_home         # => $XDG_STATE_HOME  or $HOME/.local/state
locator.cache_home         # => $XDG_CACHE_HOME  or $HOME/.cache
locator.runtime_directory  # => $XDG_RUNTIME_DIR (None if unset)
locator.data_directories   # => $XDG_DATA_DIRS   or [/usr/local/share, /usr/share]
locator.config_directories # => $XDG_CONFIG_DIRS or [/etc/xdg]

let app = locator.for_entry('myapp')

app.data_directory    # => $HOME/.local/share/myapp
app.config_directory  # => $HOME/.config/myapp
app.state_directory   # => $HOME/.local/state/myapp
app.cache_directory   # => $HOME/.cache/myapp
app.runtime_directory # => $XDG_RUNTIME_DIR/myapp (None if unset)
```

### Multi-platform

`knowndirs.multi.Locator` locates appropriate directories with the given platform and strategy.

```inko
import knowndirs.multi (EntrySpec, Locator)

let locator = Locator.new.or_panic_with('HOME must be set')
locator.platform = Platform.Apple        # or Platform.Xdg (default: Platform.compiled_for)
locator.strategy = Strategy.PreferNative # or Strategy.PreferXdg (default: PreferXdg)

locator.strategy = Strategy.PreferNative
locator.data_home # => $HOME/Library/Application Support
locator.strategy = Strategy.PreferXdg
locator.data_home # => $HOME/.local/share

let spec = EntrySpec(apple_name: 'com.example.MyApp', xdg_name: 'myapp')
let app = locator.for_entry(spec)

app.strategy = Strategy.PreferNative
app.data_directory # => $HOME/Library/Application Support/com.example.MyApp
app.strategy = Strategy.PreferXdg
app.data_directory # => $HOME/.local/share/myapp
```

## Requirements

Inko 0.20.0 or later.

## License

MPL-2.0. See [LICENSE](LICENSE).
