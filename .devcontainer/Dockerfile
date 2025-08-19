######################################################################
# Development container image
#
# This Dockerfile builds the image used by the VS Code devcontainer.
# It extends the official devcontainers base image and installs a
# collection of common build tools, language runtimes and utilities.
#
# The image is pinned to a specific digest so that builds are
# reproducible. Feel free to add or remove packages to suit your
# project's needs.
######################################################################
FROM mcr.microsoft.com/devcontainers/base:latest AS base

# Set safer shell options for script execution.
# SHELL ["/bin/bash", "-o", "errexit", "-o", "errtrace", "-o", "functrace", "-o", "nounset", "-o", "pipefail", "-c"]

# TODO: Not sure what could go here.

FROM base AS environment

# Override the default temporary directory if needed. Useful when
# working on filesystems where /tmp has limited space.
ARG TMPDIR=/tmp

# Locale and timezone settings used during build and at runtime
ARG LANG=en_US.UTF-8
ARG LANGUAGE=en_US:en
ARG TZ=UTC
ARG LC_ALL=en_US.UTF-8

# Set workspace home directory
ENV WORKSPACE_HOME=/workspace

# Location where additional development utilities are installed
ENV UNIVERSAL_HOME=/opt/universal
ENV UNIVERSAL_BIN="${UNIVERSAL_HOME}/bin"
ENV UNIVERSAL_TOOLBOX="${UNIVERSAL_HOME}/toolbox"
ENV UNIVERSAL_CACHE="${UNIVERSAL_HOME}/cache"
ENV UNIVERSAL_LOGS="${UNIVERSAL_HOME}/logs"
ENV UNIVERSAL_CONFIG="${UNIVERSAL_HOME}/config"
ENV UNIVERSAL_LIB="${UNIVERSAL_HOME}/lib"
ENV UNIVERSAL_LOCKS="${UNIVERSAL_HOME}/locks"
ENV UNIVERSAL_FONTS="${UNIVERSAL_HOME}/fonts"
ENV UNIVERSAL_RUNTIME="${UNIVERSAL_HOME}/runtime"
ENV UNIVERSAL_APPS="${UNIVERSAL_HOME}/apps"
ENV UNIVERSAL_REPORTS="${UNIVERSAL_HOME}/reports"
ENV APT_CACHE_DIR="${UNIVERSAL_CACHE}/apt"

# Core environment variables for non-interactive apt operations and consistent Python behavior.
ENV DEBIAN_FRONTEND=noninteractive \
    DEBCONF_NONINTERACTIVE_SEEN=true \
    DEBIAN_PRIORITY=critical \
    DEBCONF_NOWARNINGS=yes \
    TERM=xterm-256color \
    APT_LISTCHANGES_FRONTEND=none \
    APT_LISTBUGS_FRONTEND=none \
    TMPDIR=${TMPDIR} \
    LANG=${LANG} \
    LANGUAGE=${LANGUAGE} \
    TZ=${TZ} \
    LC_ALL=${LC_ALL} \
    PYTHONUNBUFFERED=1 \
    PYTHONIOENCODING=UTF-8 \
    PYTHONUTF8=1 \
    PYTHONHASHSEED=random \
    PIP_NO_CACHE_DIR=1 \
    PYTHONFAULTHANDLER=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=on \
    PIP_NO_WARN_SCRIPT_LOCATION=on \
    PIP_DEFAULT_TIMEOUT=200 \
    PIP_ROOT_USER_ACTION=ignore \
    CARGO_TERM_COLOR=always \
    DO_NOT_TRACK=1 \
    PYTHONWARNINGS=default \
    CLICOLOR_FORCE=1 \
    CLICOLOR=1 \
    LESS=-R \
    COLORTERM=truecolor \
    GCC_COLORS="error=01;31:warning=01;35:note=01;36:caret=01;32:locus=01:quote=01" \
    FORCE_COLOR=1 \
    EDITOR=code \
    VISUAL=code \
    PAGER=less \
    GIT_PAGER=less

# XDG Base Directories: https://wiki.archlinux.org/title/XDG_Base_Directory
ENV XDG_CONFIG_HOME=${UNIVERSAL_CONFIG} \
    XDG_CACHE_HOME=${UNIVERSAL_CACHE} \
    XDG_DATA_HOME=${UNIVERSAL_TOOLBOX} \
    XDG_STATE_HOME=${UNIVERSAL_RUNTIME} \
    XDG_CONFIG_DIRS="${UNIVERSAL_CONFIG}:/etc/xdg"

# Add user application .desktop files to gnome.
# https://help.gnome.org/admin//system-admin-guide/2.32/menustructure-desktopentry.html.en
ENV XDG_DATA_DIRS="${UNIVERSAL_APPS}:/usr/local/share:/usr/share"

# Configuration options for various tools.
ENV ASDF_DIR="${XDG_DATA_HOME}/.asdf" \
    GEM_HOME="${XDG_DATA_HOME}/ruby/gems" \
    YARN_GLOBAL_FOLDER="${XDG_DATA_HOME}/yarn" \
    ANSIBLE_HOME="${XDG_DATA_HOME}/ansible" \
    FFMPEG_DATADIR="${XDG_DATA_HOME}/.ffmpeg" \
    GOPATH="${XDG_DATA_HOME}/go" \
    VS_CODE_DATA_DIR="${XDG_DATA_HOME}/vscode/data" \
    VS_CODE_EXTENSIONS_DIR="${XDG_DATA_HOME}/vscode/extensions" \
    PYENV_ROOT="${XDG_DATA_HOME}/.pyenv" \
    POETRY_HOME="${XDG_DATA_HOME}/.poetry" \
    PIPX_HOME="${XDG_DATA_HOME}/.pipx" \
    RBENV_ROOT="${XDG_DATA_HOME}/rbenv" \
    BUNDLE_USER_PLUGIN="${XDG_DATA_HOME}/bundle" \
    RUSTUP_HOME="${XDG_DATA_HOME}/rustup" \
    GRADLE_USER_HOME="${XDG_DATA_HOME}/.gradle" \
    FLUTTER_HOME="${XDG_DATA_HOME}/flutter" \
    OSH="${XDG_DATA_HOME}/oh-my-bash" \
    ZSH="${XDG_DATA_HOME}/oh-my-zsh" \
    GNUPGHOME="${XDG_DATA_HOME}/gnupg" \
    TERMINFO="${XDG_DATA_HOME}/terminfo" \
    M2_HOME="${XDG_DATA_HOME}/mvn/.mvn" \
    MINIKUBE_HOME="${XDG_DATA_HOME}/minikube" \
    CARGO_HOME="${XDG_DATA_HOME}/.cargo" \
    WINEPREFIX="${XDG_DATA_HOME}/wine" \
    CCACHE_DIR="${XDG_DATA_HOME}/.ccache" \
    SINCE="${XDG_DATA_HOME}/since" \
    IPFS_PATH="${XDG_DATA_HOME}/ipfs" \
    NVM_DIR="${XDG_DATA_HOME}/.nvm" \
    PNPM_HOME="${XDG_DATA_HOME}/pnpm" \
    KERAS_HOME="${XDG_DATA_HOME}/keras" \
    R_HOME_USER="${XDG_DATA_HOME}/R" \
    ATOM_HOME="${XDG_DATA_HOME}/.atom" \
    KODI_DATA="${XDG_DATA_HOME}/kodi" \
    VAGRANT_HOME="${XDG_DATA_HOME}/vagrant" \
    CABAL_DIR="${XDG_DATA_HOME}/cabal" \
    AUDACITY_PATH="${XDG_DATA_HOME}/audacity" \
    BLENDER_USER_RESOURCES="${XDG_DATA_HOME}/blender" \
    DOTNET_CLI_HOME="${XDG_DATA_HOME}/dotnet" \
    ANALYZER_STATE_LOCATION_OVERRIDE="${XDG_DATA_HOME}/dart/.dartServer" \
    NUGET_PACKAGES="${XDG_DATA_HOME}/.nuget/packages" \
    MUJOCO_PY_MUJOCO_PATH="${XDG_DATA_HOME}/mujoco" \
    XMONAD_DATA_DIR="${XDG_DATA_HOME}/xmonad" \
    BINDLEPATH="${XDG_DATA_HOME}/bindle" \
    GQSTATE="${XDG_DATA_HOME}/gq/gq-state" \
    VLC_PLUGIN_PATH="${XDG_DATA_HOME}/vlc/plugins" \
    VLC_DATA_PATH="${XDG_DATA_HOME}/vlc/data" \
    ABOOK_DATA="${XDG_DATA_HOME}/abook/addressbook" \
    SDKMAN_DIR="${XDG_DATA_HOME}/sdkman" \
    PARALLEL_HOME="${XDG_DATA_HOME}/.parallel" \
    DISTCC_DIR="${XDG_DATA_HOME}/.distcc" \
    ZPLUG_HOME="${XDG_DATA_HOME}/zplug" \
    N_PREFIX="${XDG_DATA_HOME}/n" \
    UNISON="${XDG_DATA_HOME}/unison" \
    NB_DIR="${XDG_DATA_HOME}/nb" \
    OPAMROOT="${XDG_DATA_HOME}/opam" \
    PLATFORMIO_CORE_DIR="${XDG_DATA_HOME}/platformio" \
    IMAPFILTER_HOME="${XDG_DATA_HOME}/imapfilter" \
    ANKI_DIR="${XDG_DATA_HOME}/anki" \
    DOT_SAGE="${XDG_DATA_HOME}/sagemath/sage" \
    _Z_DATA="${XDG_DATA_HOME}/z" \
    BOGOFILTER_DIR="$XDG_DATA_HOME/bogofilter" \
    STACK_ROOT="${XDG_DATA_HOME}/stack" \
    TEXMACS_HOME_PATH="${XDG_DATA_HOME}/texmacs" \
    ELECTRUMDIR="${XDG_DATA_HOME}/.electrum" \
    GRIPHOME="${XDG_DATA_HOME}/grip" \
    EM_PORTS="${XDG_DATA_HOME}/emscripten/cache" \
    VOLTA_HOME="${XDG_DATA_HOME}/volta" \
    LEIN_HOME="${XDG_DATA_HOME}/lein" \
    FLATPAK_USER_DIR="${XDG_DATA_HOME}/flatpak" \
    LEDGER_FILE="${XDG_DATA_HOME}/hledger/.hledger.journal" \
    GETIPLAYERUSERPREFS="${XDG_DATA_HOME}/get_iplayer" \
    CRAWL_DIR="${XDG_DATA_HOME}/.crawl/" \
    GPODDER_DOWNLOAD_DIR="${XDG_DATA_HOME}/gpodder/downloads" \
    PEX_VERBOSE=0 \
    CALIBRE_SHOW_DEPRECATION_WARNINGS=0 \
    GHCUP_USE_XDG_DIRS="true" \
    MPV_VERBOSE="0" \
    MPV_LEAK_REPORT="0"

# Configure the ASDF version manager.
ENV ASDF_DATA_DIR="${ASDF_DIR}/data"
ENV ASDF_SHIMS_DIR="${ASDF_DATA_DIR}/shims"

ENV PIPX_BIN_DIR="${PIPX_HOME}/bin" \
    GOMODCACHE="${GOPATH}/pkg/mod" \
    TERMINFO_DIRS="${TERMINFO}:/usr/share/terminfo" \
    VAGRANT_ALIAS_FILE="${VAGRANT_HOME}/aliases"

# Set configuration files for various tools.
ENV ASDF_CONFIG_FILE="${XDG_CONFIG_HOME}/asdf/asdfrc" \
    CURLRC="${XDG_CONFIG_HOME}/curl/.curlrc" \
    WGETRC="${XDG_CONFIG_HOME}/wget/.wgetrc" \
    PIP_CONFIG_FILE="${XDG_CONFIG_HOME}/pip/pip.conf" \
    DOCKER_CONFIG="${XDG_CONFIG_HOME}/docker" \
    NPM_CONFIG_USERCONFIG="${XDG_CONFIG_HOME}/npm/.npmrc" \
    PYTHONSTARTUP="${XDG_CONFIG_HOME}/python/.pythonrc.py" \
    JUPYTER_CONFIG_DIR="${XDG_CONFIG_HOME}/jupyter" \
    IPYTHONDIR="${XDG_CONFIG_HOME}/.ipython" \
    AZURE_CONFIG_DIR="${XDG_CONFIG_HOME}/azure" \
    CALIBRE_TEMP_DIR="${TMPDIR}/calibre" \
    IDEA_PROPERTIES="${XDG_CONFIG_HOME}/intellij/idea.properties" \
    IDEA_VM_OPTIONS="${XDG_CONFIG_HOME}/intellij/idea.vmoptions" \
    ASPELL_CONF="per-conf ${XDG_CONFIG_HOME}/aspell/aspell.conf; personal ${XDG_CONFIG_HOME}/aspell/en.pws; repl ${XDG_CONFIG_HOME}/aspell/en.prepl" \
    PSQLRC="${XDG_CONFIG_HOME}/pg/psqlrc" \
    PGPASSFILE="${XDG_CONFIG_HOME}/pg/pgpass" \
    PGSERVICEFILE="${XDG_CONFIG_HOME}/pg/pg_service.conf" \
    CALIBRE_CONFIG_DIRECTORY="${XDG_CONFIG_HOME}/calibre" \
    XSEL_LOGFILE="${UNIVERSAL_LOGS}/xsel/.xsel.log" \
    ZDOTDIR="${XDG_CONFIG_HOME}/zsh" \
    BASH_COMPLETION_USER_FILE="${XDG_CONFIG_HOME}/bash-completion/bash_completion" \
    ACKRC="${XDG_CONFIG_HOME}/ack/.ackrc" \
    AWS_CONFIG_FILE="${XDG_CONFIG_HOME}/aws/config" \
    STARSHIP_CONFIG="${XDG_CONFIG_HOME}/starship.toml" \
    RIPGREP_CONFIG_PATH="${XDG_CONFIG_HOME}/ripgrep/config" \
    TRAVIS_CONFIG_PATH="${XDG_CONFIG_HOME}/travis" \
    OMNISHARPHOME="${XDG_CONFIG_HOME}/omnisharp" \
    DUB_HOME="${XDG_CONFIG_HOME}/dub" \
    EM_CONFIG="${XDG_CONFIG_HOME}/emscripten/config" \
    GR_PREFS_PATH="${XDG_CONFIG_HOME}/gnuradio" \
    TIGRC_USER="${XDG_CONFIG_HOME}/tig/tigrc" \
    XMONAD_CONFIG_DIR="${XDG_CONFIG_HOME}/xmonad" \
    GPODDER_HOME="${XDG_CONFIG_HOME}/gpodder" \
    IRBRC="${XDG_CONFIG_HOME}/irb/irbrc" \
    LYNX_CFG_PATH="${XDG_CONFIG_HOME}/lynx.cfg" \
    EASYOCR_MODULE_PATH="${XDG_CONFIG_HOME}/EasyOCR" \
    OCTAVE_SITE_INITFILE="${XDG_CONFIG_HOME}/octave/octaverc" \
    BUNDLE_USER_CONFIG="${XDG_CONFIG_HOME}/bundle" \
    DOT_SAGE="${XDG_CONFIG_HOME}/sage" \
    WEECHAT_HOME="${XDG_CONFIG_HOME}/weechat" \
    ABOOK_RC="${XDG_CONFIG_HOME}/.abook/abookrc" \
    DICTD_RC="${XDG_CONFIG_HOME}/.dictd/.dictrc" \
    GEF_RC="${XDG_CONFIG_HOME}/gef/.gef.rc" \
    CABAL_CONFIG="${XDG_CONFIG_HOME}/cabal/config" \
    RECOLL_CONFDIR="${XDG_CONFIG_HOME}/recoll" \
    KDEHOME="${XDG_CONFIG_HOME}/kde" \
    XINITRC="${XDG_CONFIG_HOME}/X11/xinitrc" \
    XSERVERRC="${XDG_CONFIG_HOME}/X11/xserverrc" \
    BINDLERC="${XDG_CONFIG_HOME}/bindlerc" \
    CONKY_CONFIG="${XDG_CONFIG_HOME}/conky/conkyrc" \
    CGDB_DIR="${XDG_CONFIG_HOME}/cgdb" \
    CLAWS_MAIL_CONFIG="${XDG_CONFIG_HOME}/claw-mails" \
    SVN_CONFIG_DIR="${XDG_CONFIG_HOME}/subversion" \
    CIN_CONFIG="${XDG_CONFIG_HOME}/cinelerra/bcast5" \
    FCEUX_HOME="${XDG_CONFIG_HOME}/fceux" \
    ELINKS_CONFDIR="${XDG_CONFIG_HOME}/elinks" \
    ELM_HOME="${XDG_CONFIG_HOME}/elm" \
    MOST_INITFILE="${XDG_CONFIG_HOME}/most/mostrc" \
    VIMPERATOR_RUNTIME="${XDG_CONFIG_HOME}/vimperator" \
    TS3_CONFIG_DIR="${XDG_CONFIG_HOME}/ts3client" \
    BOTO_CONFIG="${XDG_CONFIG_HOME}/boto" \
    VLCRC="${XDG_CONFIG_HOME}/vlc/vlcrc" \
    INPUTRC="${XDG_CONFIG_HOME}/readline/.inputrc" \
    GTK_RC_FILES="${XDG_CONFIG_HOME}/gtk-1.0/gtkrc" \
    GTK2_RC_FILES="${XDG_CONFIG_HOME}/gtk-2.0/gtkrc" \
    SCREENRC="${XDG_CONFIG_HOME}/screen/.screenrc" \
    NVIDIA_SETTINGS_RC="${XDG_CONFIG_HOME}/nvidia/.nvidia-settings-rc" \
    MPV_HOME="${XDG_CONFIG_HOME}/mpv" \
    NOTMUCH_CONFIG="${XDG_CONFIG_HOME}/notmuch/.notmuch-config" \
    UNCRUSTIFY_CONFIG="${XDG_CONFIG_HOME}/uncrustify/uncrustify.cfg" \
    MAXIMA_USERDIR="${XDG_CONFIG_HOME}/maxima" \
    K9SCONFIG="${XDG_CONFIG_HOME}/k9s" \
    NBRC_PATH="${XDG_CONFIG_HOME}/nbrc" \
    XCOMPOSEFILE="${XDG_CONFIG_HOME}/X11/xcompose" \
    MPLAYER_HOME="${XDG_CONFIG_HOME}/mplayer" \
    MEDNAFEN_HOME="${XDG_CONFIG_HOME}/mednafen" \
    MATHEMATICA_USERBASE="${XDG_CONFIG_HOME}/mathematica" \
    GQRC="${XDG_CONFIG_HOME}/gq/gqrc" \
    REDISCLI_RCFILE="${XDG_CONFIG_HOME}/redis/redisclirc" \
    NCMPC_CONFIG_DIR="${XDG_CONFIG_HOME}/ncmpc" \
    WAKATIME_HOME="${XDG_CONFIG_HOME}/wakatime" \
    CD_BOOKMARK_FILE="${XDG_CONFIG_HOME}/cd-bookmark/bookmarks" \
    DOOMDIR="${XDG_CONFIG_HOME}/doom" \
    R_PROFILE_USER="${XDG_CONFIG_HOME}/R/profile" \
    X3270PRO="${XDG_CONFIG_HOME}/x3270/config" \
    C3270PRO="${XDG_CONFIG_HOME}/c3270/config" \
    SINGULARITY_CONFIGDIR="${XDG_CONFIG_HOME}/singularity"

# Set cache directories for various tools.
ENV SONARLINT_USER_HOME="${XDG_CACHE_HOME}/.sonarlint" \
    NODE_COMPILER_CACHE="${XDG_CACHE_HOME}/node/compiler" \
    YARN_CACHE_FOLDER="${XDG_CACHE_HOME}/yarn" \
    PUB_CACHE="${XDG_CACHE_HOME}/.pub-cache" \
    __GL_SHADER_DISK_CACHE_PATH="${XDG_CACHE_HOME}/.nv" \
    HOMEBREW_CACHE="${XDG_CACHE_HOME}/homebrew" \
    KSCRIPT_CACHE_DIR="${XDG_CACHE_HOME}/kscript" \
    PYTHON_EGG_CACHE="${XDG_CACHE_HOME}/.python-eggs" \
    PEX_ROOT="${XDG_CACHE_HOME}/.pex" \
    MYPY_CACHE_DIR="${XDG_CACHE_HOME}/mypy" \
    CUDA_CACHE_PATH="${XDG_CACHE_HOME}/nv" \
    CALIBRE_CACHE_DIRECTORY="${XDG_CACHE_HOME}/calibre" \
    SOLARGRAPH_CACHE="${XDG_CACHE_HOME}/solargraph" \
    XCOMPOSECACHE="${XDG_CACHE_HOME}/X11/xcompose" \
    DVDCSS_CACHE="${XDG_CACHE_HOME}/dvdcss" \
    XMONAD_CACHE_DIR="${XDG_CACHE_HOME}/xmonad" \
    BUNDLE_USER_CACHE="${XDG_CACHE_HOME}/bundle" \
    GEM_SPEC_CACHE="${XDG_CACHE_HOME}/gem" \
    ICEAUTHORITY="${XDG_CACHE_HOME}/ICEauthority" \
    PYLINTHOME="${XDG_CACHE_HOME}/pylint" \
    STARSHIP_CACHE="${XDG_CACHE_HOME}/starship" \
    ALTUSERXSESSION="${XDG_CACHE_HOME}/X11/Xsession" \
    USERXSESSION="${XDG_CACHE_HOME}/X11/xsession" \
    USERXSESSIONRC="${XDG_CACHE_HOME}/X11/xsessionrc" \
    SINGULARITY_CACHEDIR="${XDG_CACHE_HOME}/singularity" \
    RUFF_CACHE_DIR="${XDG_CACHE_HOME}/ruff" \
    EM_CACHE="${XDG_CACHE_HOME}/emscripten/cache" \
    HOMEBREW_TEMP="${XDG_CACHE_HOME}/homebrew/tmp" \
    HOMEBREW_LOGS="${UNIVERSAL_LOGS}/homebrew"

# History settings
ENV HISTFILE="${XDG_CACHE_HOME}/bash_history" \
    HISTSIZE=10000 \
    NODE_REPL_HISTORY="${XDG_CACHE_HOME}/node_repl_history" \
    LESSHISTFILE="${XDG_CACHE_HOME}/less_history" \
    LESSHISTSIZE=10000 \
    REDISCLI_HISTFILE="${XDG_CACHE_HOME}/rediscli_history" \
    SQLITE_HISTORY="${XDG_CACHE_HOME}/sqlite_history" \
    PSQL_HISTORY="${XDG_CACHE_HOME}/psql_history" \
    PGSQL_HISTORY="${XDG_CACHE_HOME}/pgsql_history" \
    MYSQL_HISTFILE="${XDG_CACHE_HOME}/mysql_history" \
    MYSQL_HISTSIZE=10000 \
    CALCHISTFILE="${XDG_CACHE_HOME}/calc_history" \
    UNITS_HISTORY_FILE="${XDG_CACHE_HOME}/units_history" \
    RLWRAP_HOME="${XDG_CACHE_HOME}/rlwrap" \
    GDBHISTFILE="${XDG_CACHE_HOME}/gdb_history" \
    OCTAVE_HISTFILE="${XDG_CACHE_HOME}/octave-hsts" \
    JULIA_HISTORY="${XDG_CACHE_HOME}/julia_history" \
    R_HISTFILE="${XDG_CACHE_HOME}/R_history"

# Disable telemetry for various tools.
# https://github.com/beatcracker/toptout
ENV DO_NOT_TRACK=1 \
    ANALYTICS="no" \
    ADBLOCK="true" \
    DisableTelemetry="True" \
    TELEMETRY_ENABLED=0 \
    AZURE_CORE_COLLECT_TELEMETRY=0 \
    SAM_CLI_TELEMETRY=0 \
    GOTELEMETRY="off" \
    STNOUPGRADE=1 \
    YARN_ENABLE_TELEMETRY=false \
    NEXT_TELEMETRY_DISABLED=1 \
    GATSBY_TELEMETRY_DISABLED=1 \
    STORYBOOK_DISABLE_TELEMETRY=1 \
    DOTNET_CLI_TELEMETRY_OPTOUT=1 \
    DOTNET_EnableDiagnostics=0 \
    DOTNET_INTERACTIVE_CLI_TELEMETRY_OPTOUT=1 \
    MLDOTNET_CLI_TELEMETRY_OPTOUT="True" \
    DOTNET_SVCUTIL_TELEMETRY_OPTOUT=1 \
    CANVAS_LMS_STATS_COLLECTION="opt_out" \
    TELEMETRY_OPT_IN="" \
    ET_NO_TELEMETRY="ANY_VALUE" \
    WERF_TELEMETRY=0 \
    MSLAB_TELEMETRY_LEVEL="None" \
    ALLOW_UI_ANALYTICS="false" \
    NUKE_TELEMETRY_OPTOUT=1 \
    EARTHLY_DISABLE_ANALYTICS=1 \
    STRIPE_CLI_TELEMETRY_OPTOUT=1 \
    FEAST_TELEMETRY="False" \
    MELTANO_DISABLE_TRACKING="True" \
    QUILT_DISABLE_USAGE_METRICS="True" \
    CARBON_TELEMETRY_DISABLED=1 \
    FASTLANE_OPT_OUT_USAGE="YES" \
    GATSBY_TELEMETRY_DISABLED=1 \
    ORYX_DISABLE_TELEMETRY="true" \
    RASA_TELEMETRY_ENABLED="false" \
    CLOUDSDK_CORE_DISABLE_USAGE_REPORTING="true" \
    ALIBUILD_NO_ANALYTICS=1 \
    HOMEBREW_NO_ANALYTICS=1 \
    HOMEBREW_NO_ANALYTICS_THIS_RUN=1 \
    HOMEBREW_NO_AUTO_UPDATE=1 \
    DECK_ANALYTICS="off" \
    NG_CLI_ANALYTICS="false" \
    NG_CLI_ANALYTICS_SHARE="false" \
    INFLUXD_REPORTING_DISABLED="true" \
    MOBILE_CENTER_TELEMETRY="off" \
    ARDUINO_METRICS_ENABLED="false" \
    POWERSHELL_TELEMETRY_OPTOUT=1 \
    POWERSHELL_UPDATECHECK="Off" \
    LYNX_ANALYTICS=0 \
    DISABLE_QUICKWIT_TELEMETRY=1 \
    AUTOMAGICA_NO_TELEMETRY="1" \
    MM_LOGSETTINGS_ENABLEDIAGNOSTICS="false" \
    MM_SERVICESETTINGS_ENABLESECURITYFIXALERT="false" \
    APPCD_TELEMETRY=0 \
    BF_CLI_TELEMETRY="false" \
    DA_TEST_DISABLE_TELEMETRY=1 \
    CHOOSENIM_NO_ANALYTICS=1 \
    COCOAPODS_DISABLE_STATS="true" \
    CUBEJS_TELEMETRY="false" \
    HOOKDECK_CLI_TELEMETRY_OPTOUT=1 \
    VUEDX_TELEMETRY="off" \
    HINT_TELEMETRY="off" \
    BATECT_ENABLE_TELEMETRY="false" \
    DAGSTER_DISABLE_TELEMETRY=1 \
    TELEMETRY_DISABLED=1 \
    PROSE_TELEMETRY_OPTOUT=1 \
    SKU_TELEMETRY="false" \
    PANTS_ANONYMOUS_TELEMETRY_ENABLED="false" \
    CHECKPOINT_DISABLE=1 \
    ARM_DISABLE_TERRAFORM_PARTNER_ID="true" \
    SCOUT_DISABLE=1 \
    PULUMI_SKIP_UPDATE_CHECK="true" \
    DISABLE_CRASH_REPORT=1 \
    KICS_COLLECT_TELEMETRY=0 \
    INFRACOST_SELF_HOSTED_TELEMETRY="false" \
    INFRACOST_SKIP_UPDATE_CHECK="true" \
    DISABLE_AUTO_UPDATE="true" \
    BUGGER_OFF=1 \
    HASURA_GRAPHQL_ENABLE_TELEMETRY="false" \
    MEILI_NO_ANALYTICS="true" \
    NC_DISABLE_TELE=1 \
    ONE_CODEX_NO_TELEMETRY="True" \
    SQA_OPT_OUT="true" \
    ROCKSET_CLI_TELEMETRY_OPTOUT=1 \
    TUIST_STATS_OPT_OUT=1 \
    AUTOMATEDLAB_TELEMETRY_OPTIN=0 \
    AUTOMATEDLAB_TELEMETRY_OPTOUT=1 \
    CHEF_TELEMETRY_OPT_OUT=1 \
    DASH_DISABLE_TELEMETRY=1 \
    TYPO3_DISABLE_CORE_UPDATER=1 \
    REDIRECT_TYPO3_DISABLE_CORE_UPDATER=1 \
    IG_PRO_OPT_OUT="YES" \
    PNPPOWERSHELL_DISABLETELEMETRY="true" \
    PNPPOWERSHELL_UPDATECHECK="false" \
    SF_DISABLE_TELEMETRY="true" \
    APOLLO_TELEMETRY_DISABLED=1 \
    REPORTPORTAL_CLIENT_JS_NO_ANALYTICS="true" \
    SALTO_TELEMETRY_DISABLE=1 \
    VSTEST_TELEMETRY_OPTEDIN=0 \
    NUXT_TELEMETRY_DISABLED=1 \
    MSSQL_CLI_TELEMETRY_OPTOUT="True" \
    VAGRANT_CHECKPOINT_DISABLE=1 \
    VAGRANT_BOX_UPDATE_CHECK_DISABLE=1 \
    SFDX_DISABLE_TELEMETRY="true" \
    RESTLER_TELEMETRY_OPTOUT=1 \
    TEEM_DISABLE="true" \
    F5_ALLOW_TELEMETRY="false" \
    REACT_APP_WEBINY_TELEMETRY="false" \
    STRAPI_TELEMETRY_DISABLED="true" \
    STRAPI_DISABLE_UPDATE_NOTIFICATION="true" \
    SLS_TRACKING_DISABLED=1 \
    SLS_TELEMETRY_DISABLED=1 \
    SUGGESTIONS_OPT_OUT=1

# Node.js options for performance tuning and reporting
# https://nodejs.org/api/cli.html
ARG NODE_OPTIONS="\
  --max-old-space-size=4096 \
  --max-semi-space-size=512 \
  --report-directory=${UNIVERSAL_REPORTS}/node \
  --diagnostic-dir=${XDG_CACHE_HOME}/node/diagnostics \
"
ENV NODE_OPTIONS="${NODE_OPTIONS}"

# Taskfile installation directory
ENV TASKFILE_HOME_DIR="${UNIVERSAL_HOME}/.task"

# Prepend devtool locations to the PATH for easy access
ENV PATH="${UNIVERSAL_BIN}:${UNIVERSAL_BIN}/install:${UNIVERSAL_BIN}/tests:${UNIVERSAL_TOOLBOX}:${TASKFILE_HOME_DIR}:${ASDF_DIR}/bin:${ASDF_SHIMS_DIR}:${PATH}"

FROM environment AS shell

# Set working directory
WORKDIR ${WORKSPACE_HOME}

# Copy custom shell scripts and binaries
COPY --chown=vscode:vscode shell/bin/ ${UNIVERSAL_BIN}/
RUN chmod +x ${UNIVERSAL_BIN}/*

# COPY custom shell library
COPY --chown=vscode:vscode shell/lib/ ${UNIVERSAL_LIB}/
RUN find ${UNIVERSAL_LIB} -type f -name "*.sh" -exec chmod +x {} \;

# Copy configuration files for apt and dpkg
COPY shell/etc/dpkg/dpkg.cfg.d/*.conf /etc/dpkg/dpkg.cfg.d/
COPY shell/etc/apt/apt.conf.d/*.conf /etc/apt/apt.conf.d/

# Copy configuration files
# COPY shell/config/.gitignore_global

FROM shell AS apt-packages

# # Install common development tools and libraries
RUN --mount=type=cache,target=/var/cache/apt,sharing=locked,id=apt-cache \
    --mount=type=cache,target=/var/lib/apt,sharing=locked,id=apt-lib \
    apt-get update -qq && \
    apt-get upgrade --yes && \
    apt-get install --yes --no-install-recommends \
    ansible \
    apt-rdepends \
    bat \
    build-essential \
    ca-certificates \
    ccache \
    chktex \
    clang \
    clang-format \
    clang-tidy \
    cmake \
    cpanminus \
    curl \
    debtree \
    direnv \
    dnsutils \
    dos2unix \
    exiftool \
    ffmpeg \
    file \
    fontconfig \
    fonts-lmodern \
    fonts-noto \
    fonts-noto-cjk \
    fonts-noto-color-emoji \
    fzf \
    gawk \
    gdal-bin \
    gettext \
    ghostscript \
    git \
    git-lfs \
    gnupg \
    graphviz \
    htop \
    inkscape \
    iputils-ping \
    jq \
    less \
    libavif-dev \
    libblosc-dev \
    libboost-all-dev \
    libbz2-dev \
    libcairo2-dev \
    libcurl4-openssl-dev \
    libdb-dev \
    libde265-dev \
    libev-dev \
    libexif-dev \
    libffi-dev \
    libgdal-dev \
    libgdbm-dev \
    libgeos-dev \
    libgif-dev \
    libheif-dev \
    libimagequant-dev \
    libjpeg-dev \
    libkrb5-dev \
    liblcms2-dev \
    liblz4-dev \
    liblzma-dev \
    libncurses-dev \
    libnss3-dev \
    libopenexr-dev \
    libopenjp2-7-dev \
    libpango1.0-dev \
    libperl-dev \
    libpng-dev \
    libproj-dev \
    libraw-dev \
    libreadline-dev \
    libspatialindex-dev \
    libsqlite3-dev \
    libssl-dev \
    libtiff-dev \
    libtool \
    libvips-dev \
    libwebp-dev \
    libxi-dev \
    libxml2-dev \
    libxmlsec1-dev \
    libxslt1-dev \
    libzstd-dev \
    llvm \
    locales \
    locales-all \
    logrotate \
    lsb-release \
    lsof \
    make \
    moreutils \
    nano \
    net-tools \
    netcat-openbsd \
    ninja-build \
    nmap \
    openssh-client \
    openssl \
    pandoc \
    parallel \
    pkg-config \
    potrace \
    procps \
    proj-bin \
    psmisc \
    pwgen \
    python3 \
    python3-dev \
    python3-pip \
    python3-venv \
    ripgrep \
    rsync \
    software-properties-common \
    sqlite3 \
    strace \
    sudo \
    sysdig \
    sysstat \
    tar \
    tcpdump \
    tk-dev \
    tree \
    tzdata \
    unzip \
    uuid-dev \
    vim \
    watch \
    wget \
    xsel \
    xz-utils \
    yq \
    zlib1g-dev \
    zsh

FROM apt-packages AS fonts

#  Optional Google Fonts installation
ARG INSTALL_GOOGLE_FONTS=false
ARG GOOGLE_FONTS_SHA_COMMIT=2b5bd4077bd9269cdf3114266603372af6c3222d
ARG GOOGLE_FONTS_SHA256=e413e29c18fa727ff4d509280fd432fc0ecb1f6117eb29f2c0a87918d30fe3ad
ARG GOOGLE_FONTS_DIR="/usr/local/share/fonts/google"

ENV INSTALL_GOOGLE_FONTS=${INSTALL_GOOGLE_FONTS} \
    GOOGLE_FONTS_SHA_COMMIT=${GOOGLE_FONTS_SHA_COMMIT} \
    GOOGLE_FONTS_SHA256=${GOOGLE_FONTS_SHA256} \
    GOOGLE_FONTS_DIR=${GOOGLE_FONTS_DIR}

RUN --mount=type=cache,target=/var/cache/fonts,sharing=locked,id=google-fonts \
    bash -Eeuo pipefail -c '\
      if [[ "${INSTALL_GOOGLE_FONTS}" == "true" ]]; then \
        install-google-fonts --dest "${GOOGLE_FONTS_DIR}" --no-color && \
        fc-cache -f "${GOOGLE_FONTS_DIR}" && \
        echo "✅ Installed Google Fonts into: ${GOOGLE_FONTS_DIR}" && \
        count=$(find "${GOOGLE_FONTS_DIR}" -type f \( -iname "*.ttf" -o -iname "*.otf" -o -iname "*.ttc" -o -iname "*.otc" \) | wc -l) && \
        echo "📦 Font files found: ${count}" && \
        echo "🔎 Sample check (Roboto):" && \
        fc-list | grep -i "roboto" || true; \
      else \
        echo "📁 Skipping Google Fonts install"; \
      fi'

FROM fonts AS imagemagick

# Build-time toggles & pins
ARG INSTALL_IMAGEMAGICK=false
ARG IMAGEMAGICK_VERSION=7.1.1-47
# ✅ SHA256 for the official .tar.xz release (matches what you saw)
ARG IMAGEMAGICK_SHA256=2396cd3c4237cfbc09a89d31d1cee157ee11fbc8ec1e540530e10175cb707160
# Leave MD5 empty unless you want to pin it too
ARG IMAGEMAGICK_MD5=
# Optional explicit source URL (script has the same default internally)
ARG IMAGEMAGICK_URL="https://download.imagemagick.org/ImageMagick/download/releases/ImageMagick-${IMAGEMAGICK_VERSION}.tar.xz"

ENV INSTALL_IMAGEMAGICK="${INSTALL_IMAGEMAGICK}" \
    IMAGEMAGICK_VERSION="${IMAGEMAGICK_VERSION}" \
    IMAGEMAGICK_SHA256="${IMAGEMAGICK_SHA256}" \
    IMAGEMAGICK_MD5="${IMAGEMAGICK_MD5}" \
    IMAGEMAGICK_URL="${IMAGEMAGICK_URL}" \
    IMAGEMAGICK_PREFIX="${UNIVERSAL_TOOLBOX}/imagemagick"

# Add ImageMagick to PATH (bin symlinked too if --symlink-bin)
ENV PATH="${UNIVERSAL_TOOLBOX}/imagemagick/bin:${PATH}"

# Optional: make IM config path explicit (modules are auto-discovered)
ENV MAGICK_CONFIGURE_PATH="${IMAGEMAGICK_PREFIX}/etc/ImageMagick-7"
# (Tip: MAGICK_CODER_MODULE_PATH usually isn't needed; IM finds modules under the prefix.)

# Cache mounts: apt + sources
RUN --mount=type=cache,target=/var/cache/apt,sharing=locked,id=apt-cache \
    --mount=type=cache,target=/var/lib/apt,sharing=locked,id=apt-lib \
    --mount=type=cache,target=/var/cache/src,sharing=locked,id=src-cache \
    bash -Eeuo pipefail -c '\
      if [[ "${INSTALL_IMAGEMAGICK}" == "true" ]]; then \
        install-imagemagick \
          --version "${IMAGEMAGICK_VERSION}" \
          --sha256 "${IMAGEMAGICK_SHA256}" \
          ${IMAGEMAGICK_MD5:+--md5 "${IMAGEMAGICK_MD5}"} \
          --url    "${IMAGEMAGICK_URL}" \
          --prefix "${IMAGEMAGICK_PREFIX}" \
          --harden-policy --symlink-bin --no-color && \
        magick -version && \
        ldd "$(command -v magick)" | tee /tmp/ldd.magick && \
        identify -list format | egrep "PNG|JPEG|WEBP|HEIC|TIFF" | head -n 50; \
      else \
        echo "📁 Skipping ImageMagick install"; \
      fi'


# FROM environment AS texlive

# # Optional TeX Live installation
# ARG INSTALL_TEXLIVE=false

# # Set texlive directories.
# ENV TEXLIVE_INSTALL_PREFIX="${XDG_DATA_HOME}/texlive"
# ENV TEXMFHOME="${TEXLIVE_INSTALL_PREFIX}/texmf"
# ENV TEXMFVAR="${TEXLIVE_INSTALL_PREFIX}/.texlive/texmf-var"
# ENV TEXMFCONFIG="${TEXLIVE_INSTALL_PREFIX}/.texlive/texmf-config"
# ENV INSTALL_TEXLIVE=${INSTALL_TEXLIVE}

# # Optional TeX Live installation
# RUN if [ "${INSTALL_TEXLIVE}" = "true" ]; then \
#     apt-get install --yes --no-install-recommends texlive-full=2023.20240207-1; \
#     else \
#     echo "📁 Skipping texlive-full install"; \
#     fi

# FROM base AS dependencies

# # Disable the automatic removal of downloaded packages
# RUN rm -f /etc/apt/apt.conf.d/docker-clean

# # Fix for update-alternatives: error:
# # 'error creating symbolic link '/usr/share/man/man1/rmid.1.gz.dpkg-tmp': No such file or directory'
# # See https://github.com/debuerreotype/docker-debian-artifacts/issues/24#issuecomment-360870939
# RUN mkdir --parents /usr/share/man/man1



# # COPY --from=fonts /opt/devtools/fonts /opt/devtools/fonts
# RUN fc-cache -f -v

# # Configure the system locale and timezone
# RUN echo "LC_ALL=en_US.UTF-8" >> /etc/environment \
#     && echo "en_US.UTF-8 UTF-8" > /etc/locale.gen \
#     && echo "LANG=en_US.UTF-8" > /etc/locale.conf \
#     && locale-gen en_US.UTF-8 \
#     && update-locale LANG=en_US.UTF-8 \
#     && dpkg-reconfigure locales

# FROM dependencies AS perl

# RUN apt-get update && apt-get install --yes \
#   build-essential \
#   perl \
#   perl-base \
#   perl-modules \
#   libperl-dev \
#   libio-aio-perl

# # Install Perl modules needed for Perl Language Server
# RUN cpanm --no-interactive AnyEvent::AIO IO::AIO Perl::LanguageServer

# FROM perl AS devtools

# # Copy .tool-versions file
# COPY .tool-versions ${WORKSPACE_HOME}/.tool-versions

# # Install asdf version manager and plugins along with taskfile
# RUN /usr/bin/env bash ${UNIVERSAL_BIN}/setup-asdf

# FROM devtools AS final

# # Ensure SYS_UID_MAX and SYS_GID_MAX are set to high values, uncommented or inserted
# RUN /usr/bin/env bash ${UNIVERSAL_BIN}/set-sys-uid-max

# # Create the directory in advance so ownership can be adjusted later
# RUN mkdir -p "${UNIVERSAL_HOME}"

# # Copy and source custom shell aliases
# COPY shell/aliases /etc/profile.d/aliases.sh

# # Ensure correct permissions and sourcing
# RUN chmod 644 /etc/profile.d/aliases.sh && \
#     echo '[ -d /etc/profile.d ] && for f in /etc/profile.d/*.sh; do [ -r "$f" ] && . "$f"; done' \
#     >> /etc/bash.bashrc

# RUN echo 'source /etc/profile.d/aliases.sh' >> /etc/zsh/zshrc


FROM imagemagick AS final
