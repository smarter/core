cmake_minimum_required(VERSION 2.6)

include(CheckLibraryExists)

project(openj)

set (libj_SRCS
     a.c ab.c af.c ai.c am.c am1.c amn.c ao.c ap.c ar.c as.c au.c c.c ca.c cc.c cd.c cf.c cg.c ch.c cip.c cl.c cp.c cpdtsp.c cr.c crs.c ct.c cu.c cv.c cx.c d.c dc.c dss.c 
     dstop.c dsusp.c dtoa.c f.c f2.c i.c io.c j.c jdlllic.c k.c m.c mbx.c p.c pv.c px.c r.c rl.c rt.c s.c sc.c sl.c sn.c t.c u.c v.c v0.c v1.c v2.c va1.c va2.c va2s.c vamultsp.c 
     vb.c vbang.c vbit.c vcant.c vchar.c vcat.c vcatsp.c vcomp.c vcompsc.c vd.c vdx.c ve.c vf.c vfft.c vfrom.c vfromsp.c vg.c vgauss.c vgcomp.c vgranking.c vgsort.c vgsp.c vi.c 
     viix.c visp.c vm.c vo.c vp.c vq.c vrand.c vrep.c vs.c vsb.c vt.c vu.c vx.c vz.c w.c wc.c wn.c ws.c x.c x15.c xa.c xb.c xc.c xcrc.c xd.c xf.c xfmt.c xh.c xi.c xl.c xo.c xs.c 
     xt.c xu.c)

set(jconsole_SRCS
    jconsole.c
    jeload.c)

set(jconsole_LIBS
    m)

if (NOT DATA_INSTALL_PREFIX)
    if (UNIX)
        set(DATA_INSTALL_PREFIX "${CMAKE_INSTALL_PREFIX}/share/j")
    elseif (WIN32)
        set(DATA_INSTALL_PREFIX "${APPDATA}/j")
    endif ()
endif ()
configure_file(cmake/profile.ijs.cmake profile.ijs)

add_definitions(-DNOASM)

if (DATA_INSTALL_PREFIX)
    add_definitions(-DPROFILE_PATH="${DATA_INSTALL_PREFIX}/profile.ijs")
endif ()

if (UNIX)
    if (CMAKE_SYSTEM_PROCESSOR STREQUAL "amd64" OR CMAKE_SYSTEM_PROCESSOR STREQUAL "x86_64")
        add_definitions("-D_UNIX64")
    endif ()
endif ()

check_library_exists(dl dlopen "" HAVE_DL)
if (HAVE_DL)
    set(jconsole_LIBS ${jconsole_LIBS} dl)
endif ()

check_library_exists(readline readline "" HAVE_READLINE)
if (HAVE_READLINE)
    set(jconsole_LIBS ${jconsole_LIBS} readline)
    add_definitions("-DREADLINE")
endif ()

add_library(j SHARED ${libj_SRCS})
set_target_properties(j PROPERTIES COMPILE_FLAGS "-fPIC -O3 -fno-strict-aliasing")

add_executable(jconsole ${jconsole_SRCS})
target_link_libraries(jconsole ${jconsole_LIBS})

install(TARGETS j
        LIBRARY DESTINATION lib)

install(TARGETS jconsole
        RUNTIME DESTINATION bin)

file(GLOB j_SYSTEM
     "j/system/*")

if (DATA_INSTALL_PREFIX)
    install(FILES ${CMAKE_CURRENT_BINARY_DIR}/profile.ijs
            DESTINATION ${DATA_INSTALL_PREFIX})

    install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/j/system
            DESTINATION ${DATA_INSTALL_PREFIX})

    install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/j/addons
            DESTINATION ${DATA_INSTALL_PREFIX})
endif ()
