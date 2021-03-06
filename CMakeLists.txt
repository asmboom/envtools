
# to use openmp on osx, but you need to build gcc with brew
#CC=gcc-4.9
#CPP=g++-4.9
#CFLAGS= -std=c99 -pedantic -Wall -O3 -m64 -fstrict-aliasing -fopenmp
#CPPFLAGS= -mfpmath=sse -pedantic -Wall -O3 -m64 -fstrict-aliasing -fopenmp
#g++-4.9 -mfpmath=sse -fopenmp -O2 envtospecular.cpp -I/usr/local/include -L/usr/local/lib -ltiff -ljpeg -lpng -lz -lm




cmake_minimum_required(VERSION 2.8)
project(envtools)


set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${PROJECT_SOURCE_DIR}/cmake/modules)

set(Boost_USE_STATIC_LIBS OFF)
set(Boost_USE_MULTITHREADED ON)
set(Boost_USE_STATIC_RUNTIME OFF)

#set(CMAKE_C_FLAGS "-std=c99")
#set(CMAKE_CXX_FLAGS "-std=c++11" )

find_package(OpenMP)
if (OPENMP_FOUND)
    set (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
    set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
endif()



find_package(Boost COMPONENTS system )
find_package(TBB)

find_package(PNG)
find_package(TIFF)
find_package(JPEG)

find_package(OpenImageIO)

include_directories(${OIIO_INCLUDE_DIR})
include_directories( ${TIFF_INCLUDE_DIR} )
include_directories( ${TBB_INCLUDE_DIR} )

add_executable(envremap envremap.cpp)
target_link_libraries(envremap ${PNG_LIBRARY} ${TIFF_LIBRARY} ${JPEG_LIBRARY} )

install(TARGETS envremap
  RUNTIME DESTINATION bin
)


add_executable(envIrradiance envIrradiance.cpp Cubemap.cpp )
target_link_libraries(envIrradiance ${TBB_LIBRARIES} ${PNG_LIBRARY} ${TIFF_LIBRARY} ${JPEG_LIBRARY} ${OIIO_LIBRARY} ${Boost_LIBRARIES} )

install(TARGETS envIrradiance
  RUNTIME DESTINATION bin
)

add_executable(cubemapPacker cubemapPacker.cpp  Cubemap.cpp)
target_link_libraries(cubemapPacker ${TBB_LIBRARIES} ${PNG_LIBRARY} ${OIIO_LIBRARY} ${Boost_LIBRARIES} )

install(TARGETS cubemapPacker
  RUNTIME DESTINATION bin
)

add_executable(extractLight extractLight.cpp Cubemap.cpp)
target_link_libraries(extractLight ${TBB_LIBRARIES} ${PNG_LIBRARY} ${OIIO_LIBRARY} ${Boost_LIBRARIES} )

install(TARGETS extractLight
  RUNTIME DESTINATION bin
)

add_executable(extractLights ExtractLightsVariance.cpp)
target_link_libraries(extractLights ${TBB_LIBRARIES} ${PNG_LIBRARY} ${OIIO_LIBRARY} ${Boost_LIBRARIES} )

install(TARGETS extractLights
  RUNTIME DESTINATION bin
)


add_executable(envPrefilter envPrefilter.cpp Cubemap.cpp)
target_link_libraries(envPrefilter ${TBB_LIBRARIES} ${OIIO_LIBRARY} ${Boost_LIBRARIES} )

install(TARGETS envPrefilter
  RUNTIME DESTINATION bin
)


add_executable(envBackground envBackground.cpp Cubemap.cpp)
target_link_libraries(envBackground ${TBB_LIBRARIES} ${OIIO_LIBRARY} ${Boost_LIBRARIES} )

install(TARGETS envBackground
  RUNTIME DESTINATION bin
)

add_executable(samplesGGX samplesGGX.cpp Cubemap.cpp)
target_link_libraries(samplesGGX ${TBB_LIBRARIES} ${OIIO_LIBRARY} ${Boost_LIBRARIES} )

install(TARGETS samplesGGX
  RUNTIME DESTINATION bin
)

add_executable(envBRDF envBRDF.cpp)
target_link_libraries(envBRDF ${TBB_LIBRARIES} )

install(TARGETS envBRDF
  RUNTIME DESTINATION bin
)

add_executable(panoramaPacker panoramaPacker.cpp)
target_link_libraries(panoramaPacker ${TBB_LIBRARIES} ${OIIO_LIBRARY} ${Boost_LIBRARIES} )

install(TARGETS panoramaPacker
  RUNTIME DESTINATION bin
)

install(PROGRAMS process_environment.py prefilter_opencl.py ggx.cl
  DESTINATION bin
)
