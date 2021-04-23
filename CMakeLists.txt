#vivado's version of cmake is 3.3
cmake_minimum_required(VERSION 3.3)
#do not allow in source build
set(CMAKE_DISABLE_SOURCE_CHANGES ON)
set(CMAKE_DISABLE_IN_SOURCE_BUILD ON)

project(project)
set(partname "XC7Z020-1CLG484")
set(boardname "xilinx.com:zc702:part0:1.4")

include(../../common/vivadoProjGen_functions.cmake)
include(../../common/cmake_functions.cmake)
file(GLOB hdlfilelistname ipxact_files/myip/filelist.txt)
read_filelist(hdlfiles ${hdlfilelistname})

file(MAKE_DIRECTORY ${CMAKE_BINARY_DIR}/${partname}/xcidir/)

file(GLOB xcifile xcidir/*.xci)
copy_vivado_xcifile(
    XCIPATH ${xcifile}
    PARTNAME ${partname}
    DESTDIR ${CMAKE_BINARY_DIR}/${partname}/xcidir/
    XCI_OUTPUT xcifile
    )


add_vivado_xact_ip(
  IPNAME myip
  PARTNAME ${partname}
  SUBDIRNAME test
  VHDLFILES ${hdlfiles}
  TOPNAME myip_v1_0
  IP_STAMPOUTPUT ipxact_target
  )
      
add_custom_target(test_ip ALL
  DEPENDS ${ipxact_target}
  )

file(GLOB bdscript scripts/*_bd.tcl)
add_vivado_bd_hdf(
  PRJNAME test
  PARTNAME ${partname}
  BDSCRIPT ${bdscript}
  BOARDNAME ${boardname}
  XCIFILES_GEN ${xcifile}
  HDFFILE_OUTPUT hdf_target
  DEPENDS ${ipxact_target}
  )

add_custom_target(the_hdf
  DEPENDS ${hdf_target}
  )


find_program (TCLSH tclsh)


