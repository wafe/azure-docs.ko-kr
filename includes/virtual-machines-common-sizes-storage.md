저장소 최적화 VM 크기는 높은 디스크 처리량 및 IO를 제공하고 빅 데이터, SQL 및 NoSQL 데이터베이스에 적합합니다. 이 문서에서는 이 그룹화에서 각 크기에 대한 저장소 처리량 및 네트워크 성능뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다. 

Ls 시리즈는 [Intel® Xeon® 프로세서 E5 v3 제품군](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html)을 사용하여 최대 32개의 vCPU를 제공합니다. Ls 시리즈는 G/GS 시리즈와 CPU 성능이 동일하며 vCPU당 8GiB 메모리가 제공됩니다.  

## <a name="ls-series"></a>Ls 시리즈

ACU: 180-240
 
| 크기          | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시된 임시 저장소 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC 수 / 예상 네트워크 성능(Mbps) | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s  | 4    | 32   | 678   | 8              | 해당 없음/해당 없음(0)          | 5,000/125                               | 2 / 4000       | 
| Standard_L8s  | 8    | 64   | 1,388 | 16             | 해당 없음/해당 없음(0)          | 10,000/250                              | 4 / 8000  | 
| Standard_L16s | 16   | 128  | 2,807 | 32             | 해당 없음/해당 없음(0)          | 20,000/500                              | 8 / 6000 - 16000 &#8224; | 
| Standard_L32s* | 32 | 256  | 5,630 | 64             | 해당 없음/해당 없음(0)          | 40,000/1,000                            | 8 / 20000 | 
 

Ls 시리즈 VM에서 가능한 최대 디스크 처리량은 연결된 디스크의 수, 크기 및 스트라이핑에 따라 제한될 수 있습니다. 자세한 내용은 [Premium Storage: Azure 가상 컴퓨터 작업을 위한 고성능 저장소](../articles/virtual-machines/windows/premium-storage.md)를 참조하세요. 

*인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.

