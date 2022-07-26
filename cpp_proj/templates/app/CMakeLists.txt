set(APP_SOURCES
  main.cpp
)

add_executable({{app.specs.target}} ${APP_SOURCES})

{% if lib is defined%}
# Link local libraries
target_link_libraries({{app.specs.target}} PUBLIC {{lib.specs.dir}})

{% endif %}
# Link string format library
{% if 'fmt' in extern%}
target_link_libraries({{app.specs.target}} PUBLIC fmt)

{% endif %}
{% if 'spdlog' in extern%}
# Link logging library
target_link_libraries({{app.specs.target}} PUBLIC spdlog)

{% endif %}
{% if 'cxxopts' in extern%}
# Link command line option library
target_link_libraries({{app.specs.target}} PUBLIC cxxopts)

{% endif %}
{% if 'wxwidgets' in extern%}
{% if extern.wxwidgets.specs.local==True%}
# Link GUI library
include(${wxWidgets_USE_FILE})
target_link_libraries({{app.specs.target}} PUBLIC ${wxWidgets_LIBRARIES})

{% else %}
# Link GUI library
target_link_libraries({{app.specs.target}} PUBLIC wx::net wx::core wx::base)

{% endif %}
{% endif %}
{% if 'nlohmann_json' in extern%}
# Link GUI library
target_link_libraries({{app.specs.target}} PUBLIC nlohmann_json)

{% endif %}
{% if 'sqlitecpp' in extern%}
# Link GUI library
target_link_libraries({{app.specs.target}} PUBLIC SQLiteCpp)
{% endif %}
