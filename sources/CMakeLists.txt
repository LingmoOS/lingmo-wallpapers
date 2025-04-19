cmake_minimum_required(VERSION 3.5)
project(unsplash-wallpapers)

file(GLOB_RECURSE SRCS_R "*.jpg" "*.png")

set(REGULAR_FILES)
set(SYMLINKS)

foreach(filepath ${SRCS_R})
    string(REPLACE "${CMAKE_CURRENT_SOURCE_DIR}/" "" relpath ${filepath})
    if(IS_SYMLINK ${filepath})
        list(APPEND SYMLINKS ${relpath})
    else()
        list(APPEND REGULAR_FILES ${relpath})
    endif()
endforeach()

install(FILES ${REGULAR_FILES} DESTINATION /usr/share/backgrounds/lingmoos)

foreach(symlink ${SYMLINKS})
    file(READ_SYMLINK ${CMAKE_CURRENT_SOURCE_DIR}/${symlink} target)
    get_filename_component(dir ${symlink} DIRECTORY)
    install(CODE "
        file(MAKE_DIRECTORY \"\${CMAKE_INSTALL_PREFIX}/usr/share/backgrounds/lingmoos/${dir}\")
        execute_process(COMMAND \${CMAKE_COMMAND} -E create_symlink \"${target}\" \"\${CMAKE_INSTALL_PREFIX}/usr/share/backgrounds/lingmoos/${symlink}\")
    ")
endforeach()
