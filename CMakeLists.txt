cmake_minimum_required(VERSION 3.12)
project(aliddns)

# set c++ standard
set(CMAKE_CXX_STANDARD 11)

# strip the release version
set(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} -s")
set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -s")

# add source files
add_executable(aliddns
        aliddns.cpp
)

# add link libraries
target_link_libraries(aliddns
        alibabacloud-sdk-alidns
        alibabacloud-sdk-core
        jsoncpp
        curl
)

install(TARGETS aliddns
        RUNTIME DESTINATION /usr/local/bin  # 可执行文件安装路径
        )
