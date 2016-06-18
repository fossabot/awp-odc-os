##
# @author Alexande Breuer (anbreuer AT ucsd.edu)
#
# @section DESCRIPTION
# SCons build file of AWP.
#
# @section LICENSE
# Copyright (c) 2016, Regents of the University of California
# All rights reserved.
#
# Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:
#
# 1. Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.
#
# 2. Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
##

import os

# configuration
vars = Variables()

vars.AddVariables(
  EnumVariable( 'parallelization',
                'parallelization',
                'mpi_cuda',
                 allowed_values=('mpi_cuda', 'mpi_omp')
              )
)

vars.AddVariables(
  PathVariable( 'cudaToolkitDir', 'directory of CUDA toolkit', None ),
)

# include environment
env = Environment( variables = vars, tools=['default', 'intelc'] )

# generate help message
Help( vars.GenerateHelpText(env) )

# setup environment
env['ENV'] = os.environ

# add cuda support if requested
if env['parallelization'] in ['cuda', 'mpi_cuda' ]:
  if 'cudaToolkitDir' not in env:
    print('*** cudaToolkitDir not set; defaulting to /usr/local/cuda')
    env['cudaToolkitDir'] = '/usr/local/cuda'

  env.Tool('nvcc', toolpath=['tools/scons'])
  env.Append( CPPPATH = [ env['cudaToolkitDir']+'/include' ] )
  env.Append( LIBPATH = [ env['cudaToolkitDir']+'/lib64' ] )
  env.Append(LIBS = ['cudart'])

# chose compilers
env.Append( CPPDEFINES = ['ALIGNMENT=64'] )
if env['parallelization'] in ['cuda']:
  env.Append( CPPDEFINES = ['USE_CUDA'] )
  if( 'CC' in env['ENV'].keys()):
    env['CC'] = env['ENV']['CC']
  if( 'CXX' in env['ENV'].keys()):  
    env['CXX'] = env['ENV']['CXX']
elif env['parallelization'] in ['mpi_cuda']:
   env.Append( CPPDEFINES = ['USE_CUDA'] )
   env.Append( CPPDEFINES = ['USE_MPI'] )
   env['CC'] = 'mpicc'
   env['CXX'] = 'mpicxx'
elif env['parallelization'] in ['mpi_omp']:
   env.Append( CPPDEFINES = ['USE_MPI'] )
   #env.Append( CPPDEFINES = ['ALIGNMENT=64'] )
   env['CC'] = 'mpicc'
   env['CXX'] = 'mpicxx'

# add current path to seach path
env.Append( CPPPATH = [Dir('#.').path, Dir('#./src')] )

# enable c++11
env.Append( CXXFLAGS="-std=c++11" )

# check for debug mode
if ARGUMENTS.get('debug', 0):
   env.Append( CCFLAGS = ['-g','-O0'] )

# add math library for gcc
env.Append(LIBS=['m'])

# print welcome message
print( 'Running build script of AWP.' )

VariantDir('bin', 'src')

Export('env')
SConscript('bin/SConscript')