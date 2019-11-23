# 源码根路径
set(INCROOT ${PROJECT_SOURCE_DIR}/3rdparty/LuaJIT/src)
set(SRCROOT ${PROJECT_SOURCE_DIR}/3rdparty/LuaJIT/src)




IF(NOT CMAKE_BUILD_TYPE)
    SET(CMAKE_BUILD_TYPE "Release")
ENDIF(NOT CMAKE_BUILD_TYPE)

message(${CMAKE_BUILD_TYPE})

if( CMAKE_BUILD_TYPE MATCHES "Debug")	
	add_definitions(
		-DDEBUG
    )    
elseif(CMAKE_BUILD_TYPE MATCHES "Release")
    add_definitions(        
        -DNDEBUG
    )      
    #set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /Zi /sdl /Oi /GL /EHa /Gy")
endif()


# vs 工程名

add_executable(minilua ${SRCROOT}/host/minilua.c)
target_compile_definitions(minilua PRIVATE "-D_CRT_SECURE_NO_DEPRECATE" "-D_CRT_STDIO_INLINE=__declspec\(dllexport\)__inline")
add_custom_command(TARGET minilua POST_BUILD COMMAND minilua ${SRCROOT}\\..\\dynasm\\dynasm.lua -LN -D WIN -D JIT -D FFI -o
    ${SRCROOT}\\host\\buildvm_arch.h 
    ${SRCROOT}\\vm_x86.dasc
)


add_executable(buildvm 
    ${SRCROOT}/host/buildvm.c
    ${SRCROOT}/host/buildvm_asm.c
    ${SRCROOT}/host/buildvm_fold.c
    ${SRCROOT}/host/buildvm_lib.c
    ${SRCROOT}/host/buildvm_peobj.c
)
target_compile_definitions(buildvm PRIVATE "-D_CRT_SECURE_NO_DEPRECATE" "-D_CRT_STDIO_INLINE=__declspec\(dllexport\)__inline")
target_compile_options(buildvm PRIVATE "/arch:SSE2")
target_include_directories(buildvm PRIVATE 
    ${INCROOT}
    ${INCROOT}/../dynasm
)

add_custom_command(TARGET buildvm POST_BUILD COMMAND buildvm -m peobj -o ${SRCROOT}/lj_vm.obj)
add_custom_command(TARGET buildvm POST_BUILD COMMAND buildvm -m bcdef -o 
    ${SRCROOT}/lj_bcdef.h 
    ${SRCROOT}/lib_base.c 
    ${SRCROOT}/lib_math.c 
    ${SRCROOT}/lib_bit.c 
    ${SRCROOT}/lib_string.c 
    ${SRCROOT}/lib_table.c 
    ${SRCROOT}/lib_io.c 
    ${SRCROOT}/lib_os.c 
    ${SRCROOT}/lib_package.c 
    ${SRCROOT}/lib_debug.c 
    ${SRCROOT}/lib_jit.c 
    ${SRCROOT}/lib_ffi.c
)

add_custom_command(TARGET buildvm POST_BUILD COMMAND buildvm -m ffdef -o 
    ${SRCROOT}/lj_ffdef.h 
    ${SRCROOT}/lib_base.c 
    ${SRCROOT}/lib_math.c 
    ${SRCROOT}/lib_bit.c 
    ${SRCROOT}/lib_string.c 
    ${SRCROOT}/lib_table.c 
    ${SRCROOT}/lib_io.c 
    ${SRCROOT}/lib_os.c 
    ${SRCROOT}/lib_package.c 
    ${SRCROOT}/lib_debug.c 
    ${SRCROOT}/lib_jit.c 
    ${SRCROOT}/lib_ffi.c
)

add_custom_command(TARGET buildvm POST_BUILD COMMAND buildvm -m libdef -o
    ${SRCROOT}/lj_libdef.h 
    ${SRCROOT}/lib_base.c 
    ${SRCROOT}/lib_math.c 
    ${SRCROOT}/lib_bit.c 
    ${SRCROOT}/lib_string.c 
    ${SRCROOT}/lib_table.c 
    ${SRCROOT}/lib_io.c 
    ${SRCROOT}/lib_os.c 
    ${SRCROOT}/lib_package.c 
    ${SRCROOT}/lib_debug.c 
    ${SRCROOT}/lib_jit.c 
    ${SRCROOT}/lib_ffi.c
)

add_custom_command(TARGET buildvm POST_BUILD COMMAND buildvm -m recdef -o 
    ${SRCROOT}/lj_recdef.h 
    ${SRCROOT}/lib_base.c 
    ${SRCROOT}/lib_math.c 
    ${SRCROOT}/lib_bit.c 
    ${SRCROOT}/lib_string.c 
    ${SRCROOT}/lib_table.c 
    ${SRCROOT}/lib_io.c 
    ${SRCROOT}/lib_os.c 
    ${SRCROOT}/lib_package.c 
    ${SRCROOT}/lib_debug.c 
    ${SRCROOT}/lib_jit.c 
    ${SRCROOT}/lib_ffi.c
)

add_custom_command(TARGET buildvm POST_BUILD COMMAND buildvm -m vmdef -o 
    ${SRCROOT}/jit/vmdef.lua 
    ${SRCROOT}/lib_base.c 
    ${SRCROOT}/lib_math.c 
    ${SRCROOT}/lib_bit.c 
    ${SRCROOT}/lib_string.c 
    ${SRCROOT}/lib_table.c 
    ${SRCROOT}/lib_io.c 
    ${SRCROOT}/lib_os.c 
    ${SRCROOT}/lib_package.c 
    ${SRCROOT}/lib_debug.c 
    ${SRCROOT}/lib_jit.c 
    ${SRCROOT}/lib_ffi.c
    
)

add_custom_command(TARGET buildvm POST_BUILD COMMAND buildvm -m folddef -o 
    ${SRCROOT}/lj_folddef.h 
    ${SRCROOT}/lj_opt_fold.c
)




add_library(lua51 SHARED 
    ${SRCROOT}/lj_alloc.c
    ${SRCROOT}/lj_api.c
    ${SRCROOT}/lj_asm.c
    ${SRCROOT}/lj_bc.c
    ${SRCROOT}/lj_bcread.c
    ${SRCROOT}/lj_bcwrite.c
    ${SRCROOT}/lj_buf.c
    ${SRCROOT}/lj_carith.c
    ${SRCROOT}/lj_ccall.c
    ${SRCROOT}/lj_ccallback.c
    ${SRCROOT}/lj_cconv.c
    ${SRCROOT}/lj_cdata.c
    ${SRCROOT}/lj_char.c
    ${SRCROOT}/lj_clib.c
    ${SRCROOT}/lj_cparse.c
    ${SRCROOT}/lj_crecord.c
    ${SRCROOT}/lj_ctype.c
    ${SRCROOT}/lj_debug.c
    ${SRCROOT}/lj_dispatch.c
    ${SRCROOT}/lj_err.c
    ${SRCROOT}/lj_ffrecord.c
    ${SRCROOT}/lj_func.c
    ${SRCROOT}/lj_gc.c
    ${SRCROOT}/lj_gdbjit.c
    ${SRCROOT}/lj_ir.c
    ${SRCROOT}/lj_lex.c
    ${SRCROOT}/lj_lib.c
    ${SRCROOT}/lj_load.c
    ${SRCROOT}/lj_mcode.c
    ${SRCROOT}/lj_meta.c
    ${SRCROOT}/lj_obj.c
    ${SRCROOT}/lj_opt_dce.c
    ${SRCROOT}/lj_opt_fold.c
    ${SRCROOT}/lj_opt_loop.c
    ${SRCROOT}/lj_opt_mem.c
    ${SRCROOT}/lj_opt_narrow.c
    ${SRCROOT}/lj_opt_sink.c
    ${SRCROOT}/lj_opt_split.c
    ${SRCROOT}/lj_parse.c
    ${SRCROOT}/lj_profile.c
    ${SRCROOT}/lj_record.c
    ${SRCROOT}/lj_snap.c
    ${SRCROOT}/lj_state.c
    ${SRCROOT}/lj_str.c
    ${SRCROOT}/lj_strfmt.c
    ${SRCROOT}/lj_strfmt_num.c
    ${SRCROOT}/lj_strscan.c
    ${SRCROOT}/lj_tab.c
    ${SRCROOT}/lj_trace.c
    ${SRCROOT}/lj_udata.c
    ${SRCROOT}/lj_vmevent.c
    ${SRCROOT}/lj_vmmath.c
    ${SRCROOT}/lib_aux.c
    ${SRCROOT}/lib_base.c    
    ${SRCROOT}/lib_bit.c
    ${SRCROOT}/lib_debug.c
    ${SRCROOT}/lib_ffi.c
    ${SRCROOT}/lib_init.c
    ${SRCROOT}/lib_io.c
    ${SRCROOT}/lib_jit.c
    ${SRCROOT}/lib_math.c
    ${SRCROOT}/lib_os.c
    ${SRCROOT}/lib_package.c
    ${SRCROOT}/lib_string.c
    ${SRCROOT}/lib_table.c
    ${SRCROOT}/lj_vm.obj
)


target_compile_definitions(lua51 PRIVATE "_CRT_SECURE_NO_DEPRECATE" "_CRT_STDIO_INLINE=__declspec\(dllexport\)__inline" "LUA_BUILD_AS_DLL")
target_compile_options(lua51 PRIVATE "/arch:SSE2")

#cl /nologo /c /O2 /W3 /D_CRT_SECURE_NO_DEPRECATE /D_CRT_STDIO_INLINE=__declspec(dllexport)__inline /arch:SSE2 /MD /DLUA_BUILD_AS_DLL lj_*.c lib_*.c

add_executable(luajit ${SRCROOT}/luajit.c)
target_compile_definitions(luajit PRIVATE "_CRT_SECURE_NO_DEPRECATE" "_CRT_STDIO_INLINE=__declspec\(dllexport\)__inline")
target_compile_options(luajit PRIVATE "/arch:SSE2")
target_link_libraries(luajit lua51)


set_property(GLOBAL PROPERTY USE_FOLDERS ON)
set_property(TARGET minilua PROPERTY FOLDER "LuaJIT")
set_property(TARGET buildvm PROPERTY FOLDER "LuaJIT")
set_property(TARGET lua51 PROPERTY FOLDER "LuaJIT")
set_property(TARGET luajit PROPERTY FOLDER "LuaJIT")

