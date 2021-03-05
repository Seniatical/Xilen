import psutil
from apscheduler.schedulers.background import BackgroundScheduler
import os

class LimitError(Exception):
    pass
    
class Error(Exception):
    pass

class CPU_Limit:
    def __init__(self, sched: BackgroundScheduler = None):
        self.sched = sched or BackgroundScheduler()
        self.limit = 100    ## AKA max
        self.pid = os.getpid()
        self.job = None
        self.start = None
        self.pause = False
        self.stopped = False
        
    def __monitor(self, limit, pid):
        import psutil
        while True:
            if psutil.cpu_percent > limit:
                pid = psutil.Process(pid)
                pid.terminate()
    
    def percent(self):
        return psutil.cpu_percent()
        
    def set_limit(self, limit: int):
        assert type(limit) == int
        
        if limit > 100 or limit < 100:
            raise LimitError('Limit to set cannot be greater than 100 or less than 0')
        self.limit = limit
        return limit
        
    def start(self):
        if not self.start:
            self.sched.start()
            self.start = True
            return True
        raise Error('This job process is already running, use monitor() instead')
        
    def monitor(self):
        if not self.job and self.start:
            job = self.sched.add_job(self.__monitor, args=(self.limit, self.pid))
            self.job = job
            return True
        raise Error('Job already running or event hasn\'t been started')
        
     def pause(self):
        if not self.pause:
            job = self.job
            self.pause = True
            job.pause()
            return True
        raise Error('Job has already been paused')
        
     def resume(self):
        if self.pause:
            job = self.job
            job.resume()
            self.pause = False
            return True
        raise Error('Job already running')
        
     def kill(self):
        self.sched.shutdown(wait=True)
        return True
